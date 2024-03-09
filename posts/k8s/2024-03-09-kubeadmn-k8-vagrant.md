<h1>KubeAdm Installation Vagrant Configs</h1>
* Admittedly, this was my first time messing around with `Vagrantfiles`, so bare with me if it's a bit rudimentary

<h2>KubeAdm Vagrantfile</h2>
* This is the file I used for generating a 1 master, 1 worker kubeadm cluster

```ruby
# -*- mode: ruby -*-
# vi: set ft=ruby :
IP_NW = "10.0.0."
MASTER_NODE_NUM_START = "11"
MASTER_NODE_NUM_END = "11"
WORKER_NODE_NUM_START = (MASTER_NODE_NUM_START.to_i + 1).to_s
WORKER_NODE_NUM_END = (WORKER_NODE_NUM_START.to_i + 0).to_s
MASTER_PREFIX = "0"
WORKER_PREFIX = "0"
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"
  config.vm.boot_timeout = 900

  # define masters
  (MASTER_NODE_NUM_START..MASTER_NODE_NUM_END).each do |i|
    MASTER_PREFIX = (MASTER_PREFIX.to_i + 1).to_s
    config.vm.define "controlplane0#{MASTER_PREFIX}" do |master|
      master.vm.provider "virtualbox" do |vb|
        vb.name = "controlplane0#{MASTER_PREFIX}"
        vb.memory = 2048
        vb.cpus = 2
      end
      master.vm.hostname = "controlplane0#{MASTER_PREFIX}"
      master.disksize.size = "8GB"
      master.vm.network "private_network", ip: IP_NW + "#{i}"
      master.vm.provision "shell", path: "dependencies-kubeadm.sh"
    end
  end

  # define workers
  (WORKER_NODE_NUM_START..WORKER_NODE_NUM_END).each do |i|
    WORKER_PREFIX = (WORKER_PREFIX.to_i + 1).to_s
    config.vm.define "node0#{WORKER_PREFIX}" do |worker|
      worker.vm.provider "virtualbox" do |vb|
        vb.name = "node0#{WORKER_PREFIX}"
        vb.memory = 1024
        vb.cpus = 1
      end
      worker.vm.hostname = "node0#{WORKER_PREFIX}"
      worker.disksize.size = "8GB"
      worker.vm.network "private_network", ip: IP_NW + "#{i}"
      worker.vm.provision "shell", path: "dependencies-kubeadm.sh"
    end
  end
end
```

* There's probably a better way to set up some of these scripts, but I haven't done much work with vagrant or ruby
* I'd like to see how this would work with a Bridge Network, but for now I want all of this to remain private
* I'd also like to explore a multi-master cluster with an HAproxy in front of the `kube-api-servers`

<h2>KubeAdm Provisioner Shell Script</h2>
* This is the shell script I used for generating the cluster
* Note: Still isn't fully automated, but I don't want to forget the steps I took

```console
#!/bin/bash
#
#-------------------------------------------# KubeAdm Dependencies

# Build Variables
export HOSTNAME=$(hostname)
export PRIMARY_IP=$(hostname -I | awk '{print$2}' | tr -d '\n')
export POD_CIDR="10.244.0.0/16"
export SVC_CIDR="10.96.0.0/16"

# Point to Google's DNS server
sed -i -e 's/#DNS=/DNS=8.8.8.8/' /etc/systemd/resolved.conf
service systemd-resolved restart

# Set up ssh-keys
ssh-keygen -t ed25519

# modules necessay for k8 networking and volume support
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF

# load modules
sudo modprobe overlay
sudo modprobe br_netfilter

# sysctl params required by setup, params persist across reboots
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF

# Apply sysctl params without reboot
sudo sysctl --system

# verify both modules are running
lsmod | grep br_netfilter
lsmod | grep overlay

# verify sys vars are set to 1
sysctl net.bridge.bridge-nf-call-iptables net.bridge.bridge-nf-call-ip6tables net.ipv4.ip_forward

# Installing CRT
echo "**********Installing CRT**********"
sudo apt-get update
sudo apt-get install ca-certificates curl -y
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc
echo "CRT Installation complete"

# Getting Repo
echo "**********Pulling ContainerD Package Repo**********"
echo \
"deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
$(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update

# Install containerd.io package
echo "**********Installing and Configuring Containerd.io**********"
sudo apt-get install containerd.io -y
systemctl status containerd

# cgroupsfs driver is set by default, but if your system uses systemd then switch it
echo '
[plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc]
  [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options]
    SystemdCgroup = true
' | sudo tee /etc/containerd/config.toml

sudo cat /etc/containerd/config.toml
sudo systemctl restart containerd

sudo apt-get update
# apt-transport-https may be a dummy package; if so, you can skip that package
sudo apt-get install apt-transport-https gpg -y

# download signing key for k8 package repo
echo "**********Pulling K8 Package repo**********"
# If the folder `/etc/apt/keyrings` does not exist, it should be created before the curl command, read the note below.
# sudo mkdir -p -m 755 /etc/apt/keyrings, Ubuntu 22.04 and Debian 12 do not have this dir
if ! [ -f /etc/apt/keyrings ]; then
  sudo mkdir -p -m 755 /etc/apt/keyrings
fi

sudo rm -rf /etc/apt/keyrings/kubernetes-apt-keyring.gpg
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.29/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg     

# This overwrites any existing configuration in /etc/apt/sources.list.d/kubernetes.list
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.29/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

echo "**********Installing KubeAdm Tools**********"
sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl

# set primary ip for kubelet
cat <<EOF | sudo tee /etc/default/kubelet
KUBELET_EXTRA_ARGS="--node-ip ${PRIMARY_IP}"
EOF

if [[ "$HOSTNAME" == *"controlplane"* ]]; then
  echo "**********Initalizing KubeAdm**********"
  sudo kubeadm init --pod-network-cidr "$POD_CIDR" --apiserver-advertise-address "$PRIMARY_IP" --service-network-cidr "${SVC_CIDR}"
  create config file
  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config
  # set alias
  echo "alias k='kubectl'" | sudo tee -a $HOME/.bashrc
  echo "**********Initalizing Weave**********"
  kubectl apply -f "https://reweave.azurewebsites.net/k8s/v1.25/net.yaml?env.IPALLOC_RANGE=${POD_CIDR}"
fi

# Add routing to kubernetes service
sudo ip route add "${SVC_CIDR}" dev enp0s8 proto kernel scope link src "${PRIMARY_IP}"
```

* There probably is a better way to share ssh keys between these nodes
