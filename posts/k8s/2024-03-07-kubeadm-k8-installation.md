<h1>KubeAdm: Kubernetes Cluster</h1>
* Installing a Kubernetes Cluster using KubeAdm
* There are a couple of prerequisites that need to be completed before we can begin
  1. Install a `Container Runtime` prereqs
   
     ```console
     # Run on all nodes
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
    ```

  2. Select and Install a `CRT`
 
     ```console
     # Using ContainerD
     # Add Docker's official GPG key:
     sudo apt-get update
     sudo apt-get install ca-certificates curl
     sudo install -m 0755 -d /etc/apt/keyrings
     sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
     sudo chmod a+r /etc/apt/keyrings/docker.asc

     # Add the repository to Apt sources:
     echo \
     "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
     $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
     sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
     sudo apt-get update
    
     # Install containerd.io package
     sudo apt-get install containerd.io -y
     systemctl status containerd
    
     # configure systemd cgroup driver (if system is using systemd)
     # cgroupsfs driver is set by default, but if your system uses systemd then switch it
     ps -p 1
     sudo vi /etc/containerd/config.tomal
     [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc]
       [plugins."io.containerd.grpc.v1.cri".containerd.runtimes.runc.options]
         SystemdCgroup = true
     ``` 

  3. Install `Kubeadm`, `kubelet`, and `kubectl`
  
     ```console
     sudo apt-get update
     # apt-transport-https may be a dummy package; if so, you can skip that package
     sudo apt-get install -y apt-transport-https ca-certificates curl gpg

     # download signing key for k8 package repo     
     # If the folder `/etc/apt/keyrings` does not exist, it should be created before the curl command, read the note below.
     # sudo mkdir -p -m 755 /etc/apt/keyrings, Ubuntu 22.04 and Debian 12 do not have this dir
     curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.29/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
  
     sudo apt-get update
     sudo apt-get install -y kubelet kubeadm kubectl
     sudo apt-mark hold kubelet kubeadm kubectl
     # kubelet will restart every few seconds as it waits for kubeadm to tell it what to do
     ```

  4. Initialize the control-plane node
  
     ```console
     # Run kubeadm init
     # Run only on master node
     # --control-plane-endpoint <ip-or-dns-of-lb> that will balance traffic between master nodes
     # --cri-socket
     # --pod-network-cidr cidr for all pods
     # --apiserver-advertise-addres <ip-of-api-server> can also be lb that will balance traffic
     sudo kubeadm init --pod-network-cidr 10.244.0.0/16 --apiserver-advertise-address 10.0.0.11
    
     # create config file
     mkdir -p $HOME/.kube
     sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
     sudo chown $(id -u):$(id -g) $HOME/.kube/config
     ```
  5. Deploy Pod Network to the Cluster
  
     ```console
     # Test adding 8.8.8.8 nameserver to /etc/resolv.conf if it fails ¯_(ツ)_/¯
     kubectl apply -f https://reweave.azurewebsites.net/k8s/v1.28/net.yaml
     
     # Set IPALLOC_RANGE var to pod-networking-cidr in weave-net ds
     k edit ds weave-net -n kube-system
     # Verify
     kubectl get pods -n kube-system -o wide
     ```
  6. Join Worker Nodes to Cluster
  
     ```console
     sudo kubeadm join xxxxx:6443 --token xxxx \
         --discovery-token-ca-cert-hash sha256:xxxxx
     # if token expires, which it will after 24hrs, generate a new one
     kubeadm token create
     ```

