<h1>Ansible Plugins</h1>

* `Ansible Plugins` all you to extend the functionality of `Ansible`, make it easier to tailor it to your specific needs
  - These plugins include modules, many of which are already built into `Ansible`
  - I want to note a few plugins that I feel may be very useful
* Types of `Ansible Plugins` that I feel are the Most important:
  - `Inventory Plugins`
  - `Test Plugins`
  - `Var Plugins`
  * More on the different types of plugins can be found [here](https://docs.ansible.com/ansible/latest/collections/all_plugins.html)

<h2>Inventory Plugins & Modules</h2>

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

    # Create static groups
    groups:
      # use Jinja2 syntax to define
      first: "'legacy' in tags.Name"  
      latest: "'latest' in tags.Name"
    {% endraw %}
    ```
  
