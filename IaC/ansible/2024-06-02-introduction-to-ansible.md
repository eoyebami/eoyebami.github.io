<h1>Introduction to Ansible</h1>
* `Ansible` is a powerful automation tool used for vm provisioning and configuration management
* Using `Infrastructure as Code` in the form of YAML, we are able to automate software provisioning, configurations, and application deployments across multiple different hosts

<h2>Ansible Installation</h2>
* There are various ways to install `Ansible` on your system
  - `Ansible` documentation points us to the various methods [here](https://docs.ansible.com/ansible/latest/installation_guide/installation_distros.html)
  - Because I tend to work on `ubuntu/debian` machines; I will be using the following commands to install it
    
    ```yml
    $ sudo apt update
    $ sudo apt install software-properties-common
    $ sudo add-apt-repository --yes --update ppa:ansible/ansible
    $ sudo apt install ansible
    ```

<h2>Ansible Configuration Files</h2>
* `Ansible` has a configuration file defined that controls how it will function in a system
* By default, `Ansible's` configuration file; `ansible.cfg` can be found at `/etc/ansible/ansible.cfg`
* These values can be overridden in multiple different ways:
  - `Environment Var`: `$ANSIBLE_CONFIG:/path/to/ansible.cfg` define a var pointing to the ansible.cfg, this has the greatest priority when `Ansible` is determining which config to use
    * With vars, you can also set them to override one specific parameter, rather than specifying a path to an entirely new cfg
    * (i.e. gathering parameter can set via env var by exporting `ANSIBLE_GATHER=explicit`, as the environment variable)
  - `Local Config`: within each playbook you can define a customs config file for that specific playbook, this has the second highest priority when `Ansible` is determining which config to use 
  - `Home Config`: which each user's home directory, a `.ansible.cfg` file can be defined, this has third highest priority when `Ansible` is determining which config to use
  - `Default Config`: the default `ansible.cfg` found at `/etc/ansible/ansible.cfg`; least priority
* You do not need to provide the whole config in each priority, just provide the ones you need to override and `Ansible` will pick up defaults from lower priority config
* NOTE: to figure out what each config parameter does, default value, and how to pass it as an env var; run:
  - `ansible-config list`: list all configs
  - `ansible-config view`: view current configs
  - `ansible-config dump`: shows all current configs and where these configs are being defined
