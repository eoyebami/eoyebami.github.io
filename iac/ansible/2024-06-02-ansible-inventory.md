<h1>Ansible Inventory</h1>

* `Ansible` of working with a variety of servers, in order to do so it needs to establish connectivity to those machines
  - In Linux, particularly, this is done using `SSH`
  - This allows `Ansible` to work agentless, usins a push configuration
* In order to configure this inventory of vms ansible will be connecting to, you'll can either modify the default config file `/etc/ansible/hosts` or specify your own

<h2>Creating an Inventory</h2>

* The inventory file is simply just a list of the servers, by hostname, that you want `Ansible` to govern
* There are 2 types of formats you can use when configuring an inventory file:
  - `INI`
  - `YAML`
  
  ```bash
  # /etc/ansible/hosts
  example.server1.com
  example.server2.com
  example.server3.com

  [jenkins] # you can group servers into sections like this as well
  jenkins.server1.com
  jenkins.server2.com
  jenkins.server3.com

  [mysql]
  db.server1.com
  db.server2.com
  ```

  ```yml
  # /etc/ansible/hosts
  all:
    children:
      ungrouped:
        hosts:
          example.server1.com:
          example.server2.com:
          example.server3.com:
      jenkins:
        hosts:
          jenkins.server1.com:
          jenkins.server2.com:
          jenkins.server3.com:
      jenkins:
        hosts:
          db.server1.com:
          db.server2.com:
  ```

* There are a variety of options you can also define when configuring your inventory file
  - Inventory Parameters:
    * `ansible_host`: FQDN of the server
    * `ansible_port`: connection port of the server
    * `ansible_user`: user ansible should be connecting as, used by windows, default is administrator
    * `ansible_password`: password for windows machine
    * `ansible_ssh_user`: ssh user for remote host # new version, by default root user is used
    * `ansible_ssh_pass`: ssh password not recommended, instead use asymmetric keys
    * `ansible_connection`: defines how ansible should connect to host, can be `ssh` or `winrm` for either linux or windows
  
  ```bash
  jenkins   ansible_host=jenkins.server.com ansible_connection=ssh ansible_port=22 ansible_user=ubuntu
  db        ansible_host=db.server.com ansible_connection=ssh ansible_port=22 ansible_user=ec2-user
  localhost ansible_connection=local # ansible will treat host server as part of inventory

  # you can defined ungrouped servers above and call them in groups, by their alias names, below
  [jenkins]
  jenkins

  [db-servers]
  db
  ```

  ```yml
  # /etc/ansible/hosts
  ungrouped:
    hosts:
      jenkins:
        ansible_host: jenkins.server.com
        ansible_connection: ssh
        ansible_port: 22
        ansible_ssh_user: ubuntu
      example.server2.com:
        ansible_host: db.server.com
        ansible_connection: ssh
        ansible_port: 22
        ansible_ssh_user: ec2-user
      localhost:
        ansible_connection: local
  ```

* More information about configuring inventory files can be found [here](https://docs.ansible.com/ansible/latest/inventory_guide/intro_inventory.html)
