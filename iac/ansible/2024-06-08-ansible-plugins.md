<h1>Ansible Plugins</h1>

* `Ansible Plugins` all you to extend the functionality of `Ansible`, make it easier to tailor it to your specific needs
  - These plugins include modules, many of which are already built into `Ansible`
  - I want to note a few plugins that I feel may be very useful
* Types of `Ansible Plugins` that I feel are the Most important:
  - `Inventory Plugins`
  - `Test Plugins`
  * More on the different types of plugins can be found [here](https://docs.ansible.com/ansible/latest/collections/all_plugins.html)

<h2>Inventory Plugins & Modules</h2>

* `Inventory Plugins` are plugins used to modify, create, and manipulate host inventory files
  * [List of Inventory Plugins](https://docs.ansible.com/ansible/latest/collections/index_inventory.html)
* Collection: `amazon.aws`: `ansible-galaxy collection install amazon.aws`
  - [amazon.aws.aws_ec2](https://docs.ansible.com/ansible/latest/collections/amazon/aws/aws_ec2_inventory.html#ansible-collections-amazon-aws-aws-ec2-inventory): allows you to retrieve inventory hosts from AWS EC2
    * Inventory file is YAML and must end with `*.aws_ec2.{yml|yaml}$`
    * [Guide](https://docs.ansible.com/ansible/latest/collections/amazon/aws/docsite/aws_ec2_guide.html)
 
    ```yml
    {% raw %}
    #--- 
    plugin: amazon.aws.aws_ec2 # plugin in use
    regions: # regions to select hosts
    - us-west-2

    profile: aws_profile  # profile to use
    # {{ lookup('env', 'AWS_PROFILE') | default('home') }} # use lookup filter to grab env var with PROFILE
    
    filters: # filters to use when searching for hosts
    # https://docs.aws.amazon.com/cli/latest/reference/ec2/describe-instances.html#options list of filters you can use
      tag:Name: # Tag filter will be the most useful in my case
      - "*1.0.0*" # regex should be possible
   
    # exclude hosts that match filters
    exclude_filters: 
    - tag:Name:
      - '{{ non_inv_filter}}'
  
    hostnames: # define an order for precedence for host name vars, sets 'inventory_hostname
    - name: 'tag:Name' #  uses name tag as hostname
      separator: '-' # field separator
      prefix: 'private-ip-address' # prefixes hostname with private ip
    # you can also specify a list of order of precedence
    - tag:Name
    - dns-name
    - ip-address
    - private-ip-address
  
    # Create dynamic group
    keyed_groups:
    - prefix: web-servers  # prefix of group
      key: tags.Group
    # creates a group called web-servers-primary, and adds all matching hosts to group, if group is primary

    # Create static groups
    groups:
      # use Jinja2 syntax to define
      first: "'legacy' in tags.Name"  
      latest: "'latest' in tags.Name"

    # create vars using jinja2 expressions
    compose:
      # hosts will use ec2 private ips
      ansible_host: private_ip_address
     
      # connection with ssm
      ansible_host: instance_id
      ansible_connection: 'community.aws.aws_ssm'

      # set user and private key for ssh
      ansible_user: ec2-user
      ansible_ssh_private_key_file: /home/ec2-user/.ssh/id_ed25519
    {% endraw %}
    ```

<h2>Test Plugins & Modules</h2>

* `Test Plugins` allow you to determine the truthiness of a condition
  - Some modules typically have a corresponding `test` version
  - [List of Test Plugins](https://docs.ansible.com/ansible/latest/collections/index_test.html)

* Below is a list of operators that can be used for `test` plugins
  * `Logical Operators`:
    - `is`
    - `is not`

* `ansible.builtin`  
  - [ansible.builtin.file](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/file_test.html#ansible-collections-ansible-builtin-file-test): dost path resolve to an existing file
 
    ```yml
    {% raw %}
    tasks:
    - name: Create a file
      ansible.builtin.file:
        path: /etc/hosts
    when: {{ '/etc/hosts' is not ansible.builtin.file }}
    {% endraw %}
    ```

* There are many different types of plugins, some of which I've listed [here](https://eoyebami.github.io/iac/ansible/2024-06-07-ansible-modules.html)
* Here I've documented examples of test and inventory plugins since to me, they are the most different from the others

