<h1>Vagrantfile Configuration</h1>
* `Vagrantfiles` are configuration scripts for managing you vms, however there are numerous configs to keep track of
* Configs:
  - `Vagrant.configure("2") do |config|`: start of the vagrant code block, `2` defines the version of vagrant we are using
  - `config.vm.box = "ubuntu/bionic64`: defines which box we want to be the base of our vms
    * List of compatible boxes can be found [here](https://app.vagrantup.com/boxes/search?utf8=%E2%9C%93&sort=downloads&provider=&q=ubuntu)
    * keep in mind which provider each box is compatible with, in my case I'm using vbox
  - `config.vm.provider "virtualbox"`: specify provider 
    * `config.vm.provider.name = "master-node"`: name of vm in vbox
    * `config.vm.provider.cpus = "1"`: cpus to assign to vm
    * `config.vm.provider.memory = "1024"`: memory to assign to vm
  - `config.vm.network "forward_port", guest: 80, host: 8080, auto_correct: true`: port forwarding from vm to host machine
    * Access is now available via `localhost`
    * Specify a `host_ip: "127.0.0.1"`: to disable public access to that port
    * `auto_correct`: will change port to any in the range specified in `usable_port_range` if specified port is already in us
    * If `usable_port_range` is not specified, default is `(2200..2500)`
  - `config.vm.usable_port_range = (2200..2500)`: ports vagrant will choose from is `forward_port` is busy
  - `config.vm.synced_folder "/path/on/host", "/path/on/vm"`: syncs folder on host machine to folder in vm
    * `Vagrant` autosyncs the root directory of your `Vagrantfile` with the `/vagrant` directory in your vm
    * `config.vm.synced_folder ".", "/vagrant", disabled: true`: disables vagrant file sharing to vm
  - `config.disksize.size = "50GB"`: allows you to define disk space value for a vm
    * NOTE: requires `vagrant-disksize` plugin
    * `vagrant plugin install vagrant-disksize`
  - `config.vm.network "private_network", ip: "xxx.xxx.xxx.xxx"`: creates a private network that only host can access
    * For multiple nodes, try to contain within a cidr block
  - `config.vm.boot_timeout = 600`: timeout for vm, default is 300s
  - `config.vm.provision`: this step is used for provisioning the vm, like packages installations and scripts

    ```ruby
    # Using inline script
    config.vm.provision "shell", inline: <<-SHELL
      sudo apt-get update && sudo apt-get upgrade -y
      sudo apt install jq curl zip unzip ca-certificates -y
    SHELL
    
    # Using path
    config.vm.provision "shell", path: "bootstrap.sh" # specify a path to your script
    # Other provisioners can include Ansible, Puppet, and Chef
    ```

* You can also use a `Vagrantfile` generator found [here](https://vagrantfile-generator.vercel.app/) to auto-generate a file with your specifications
* NOTE: for any advanced configurations go [here](https://developer.hashicorp.com/vagrant/docs/vagrantfile/machine_settings)

<h2>For loops in Vagrant</h2>
* A more efficient way to generate your `Vagrantfile` is to use for loops, which are especially beneficial when creating more than one vm
- Use a range:

  ```ruby
  (1..4).each do |i|
    # lines of code
  end # finish off with an 'end' to mark end of loop
  # for the range 1-4 run the lines of code, and save the current value as i
  # the value i can be called as a var
  ```

<h2>Block Variables in Vagrant</h2>
* Defining block vars help when trying to shorten the amount of characters needed to write a line of code
- Define vm allocations under the block var vm

  ```ruby
  config.vm.provider "virtualbox" do |vm|
    vm.name = "master-node"
    vm.cpus = "1"
    vm.memory = "1024"
  end # finish off with 'end' to make end of block var
  ```

* NOTE: regular vars in ruby use this format: `#{}`
* Ex:

  ```ruby
  # -*- mode: ruby -*-
  # vi: set ft=ruby :

  Vagrant.configure("2") do |config|
    config.vm.box = "ubuntu/bionic64"
    config.vm.hostname = "master-node"
    config.vm.boot_timeout = 600
    config.disksize.size = "8GB"
    config.vm.network "private_network", ip: "xxx.xxx.xxx.xxx"
    config.vm.provider "virtualbox" do |vmach|
      vmach.name = "master-node"
      vmach.cpus = "1"
      vmach.memory = "512"
    end
  
    config.vm.provision "shell", inline: <<-SHELL
      sudo apt-get update && sudo apt-get upgrade -y
      sudo apt install jq curl zip unzip ca-certificates -y
    SHELL
  end
  ```

<h2>Multi-VM Environment</h2>
* In a multi-vm environemnt, you'll use a new config for separating vms 
  - `config.vm.define "vm-def" do |controlplane|`
* Ex:

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
        master.vm.provision "shell", inline: <<-SHELL
          sudo apt-get update && sudo apt-get upgrade -y
          sudo apt install jq curl zip unzip ca-certificates -y
        SHELL
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
        worker.vm.provision "shell", inline: <<-SHELL
          sudo apt-get update && sudo apt-get upgrade -y
          sudo apt install jq curl zip unzip ca-certificates -y
        SHELL
      end
    end
  end
  ```

* When allowing `PubKeyAuthentication` between vms, first enable `PasswordAuthentication`, then `ssh-copy-id` between vms, then disabled `PasswordAuthentication`

