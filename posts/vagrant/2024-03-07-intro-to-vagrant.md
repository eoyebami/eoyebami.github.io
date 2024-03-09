<h1>Intro to Vagrant</h1>
* `Vagrant` is a tool for building and managing virtual machine environments in a single workflow
  - Basically its a tool for managing and install vms which in my case is going to be on top of a hypervisor
* Before, getting started, make sure you have a directory where you will manage all of your vms
  - `mkdir vagrant-envs`
<h2>Initializing a Vagrant Environment</h2>
* To start up with using `vagrant`, similarly to any `hashicorp` software, you will first need to initialize it
  - `vagrant init`: initalizes a directory, and inputs a `Vagrantfile` within the folder
  - `vagrant init <vagrant-box>`: will initalize a folder with a `Vagrantfile` for that exact virtual box
  - `vagrant up`: will download and start up the virtual machine
  - `vagrant ssh`: to ssh into the vm 
  - `vagrant halt`: stops the vm
  - `vagrant suspend`: suspends the vm
  - `vagrant resume`: resumes the vm
  - `vagrant reload`: reboots the vm
    * During reload, any changes made to `Vagrantfile` provisioner, will be updated in vm
    * `vagrant reload --provision`: restarts vms with updated `Vagrantfile`
  - `vagrant destroy`: stops and deletes the vm and all traces
  - `vagrant status`: displays status of vagrant env
  - `vagrant package`: packages the vm into a reusable box
  - `vagrant provision`: runs any configured provisioners against the VM
  - `vagrant plugin install`: installs a vagrant plugin
  - `vagrant plugin list`: lists all installed vagrant plugins
    * list of possible plugins you can install can be found [here](https://github.com/hashicorp/vagrant/wiki/Available-Vagrant-Plugins)
  - `vagrant plugin uninstall`: uninstalls a vagrant plugin
  - `vagrant plugin update`: updates plugins
  - `vagrant plugin expunge`: deletes all plugins
  - `vagrant plugin expunge --reinstall`: reinstalls all expunged plugins
  - `vagrant box add <vagrant-box>`: addes vagrant box to local box repo
  - `vagrant box list`: lists all vagrant boxes in your local box repo
    * All your boxes will be stored in a `~/.vagrant.d` directory
  - `vagrant box outdated`: checks if any boxes in your repo are outdated
  - `vagrant box update <vagrant-box>`: updates a box to a new version
  - `vagrant box repackage <vagrant-box> --name <new-name>`: repackages a box with a new name and metadata
  - `vagrant box prune`: removes outdated box from repo
  - `vagrant box remove <vagrant-box>`: removes a box from your box repo
  - `vagrant global-status`: list the status of all vms in your system
  - `vagrant validate`: will check if your `Vagrantfile` is a validated file
