<h1>Ansible Variables</h1>

* `Ansible` Variables are a way to save information to a key that can be called later on within the file
  - This can be used in `playbooks`, `inventory`, and even designated `variable` files 
* There a couple ways to do it, but for now we'll use the `inventory` example

* NOTE: vars assigned to parent groups will also apply to child group
  - hosts, child groups, parent groups, and all groups can have the same vars defined
    * But the var of the child relative to a group, will always take higher priority
    * However, var defined at the playbook level will have an even higher priority
    * Vars specified with the `--extra-vars` flag at playbook runtime, will have the highest priority

  * `Variable Precedence`:
    - Role Defaults
    - Group Vars
    - Host Vars
    - Host Facts
    - Playbook Vars
    - Role Vars
    - Include Vars
    - Set Facts
    - Extra Vars

* TERMINOLOGY:
  - `Play`: a task within a playbook
  - `Playbook`: a collections of plays

<h2>Variable Types</h2>

* There are different types of variables that can be defined in `Ansible`
  - `String`: hold string values
  - `Number`: hold integer and floating point values
  - `Boolean`: hold true or false values
  - `List`: hold a list of values of any type, use indexing in Jinja templates to call an values
  - `Dictionary`: holds a map of key-value pairs, use a jsonpath like strategy to call values

<h2>Variable Scopes</h2>

* `Variable Scopes` defines a vars accessibility
  - For example, if I have a var assigned to a specific hosts, then I try to run a play against all of my servers, then I can expect my playbook to fail with `VARIABLE IS NOT DEFINED`, because it exists outside of that scope
* We have a different scopes:
  - `Host`: defined at the Host level
  - `Group`: defined at the group level
  - `Play`: defined at the play level
  - `Global`: defined at the root level

<h4>Host Scoped Vars</h4> 

  ```bash
  [web-servers]
  web1 http_port=80
  web2 http_port=8080
  ```

  ```yml
  web-servers:
    hosts:
      web1:
        http_port: 80
      web2:
        http_port: 8080
  ```

<h4>Group Scoped Vars</h4>

  ```bash
  [web-servers]
  web[1:2]

  [web-servers:vars]
  http_port=80
  https_port=443
  ```

  ```yaml
  web-servers:
    hosts:
      web[1:2]
    vars:
      http_port: 80
      https_port: 443
  ```

<h4>Play Scoped Vars</h4>

* Within a playbook, a var can also be defined
* Defining Vars in Playbook

  ```yml
  - hosts: web-servers
    vars:
      http_port: 80
  ```

<h4>Global Scoped Vars</h4>

* Defined at the root level
* These can be defined within a variable.yaml file or by passing it in using the `--extra-vars` flag at play runtime

<h2>Saving command output as a var</h2>

  - Within the var, you can pull a variety of information that you can use
    * `result.rc`: '0' if ran successfully or '1' if it failed
    * `result.start`: start time of command
    * `result.end`: end time of command
    * `result.stdout`: stdout of command
    * `result.stderr`: stderr of command
    * `result.cmd`: command ran
    * `result.failed`: returns boolean for whether or not command failed or not
  - Vars also defined this way are host scoped, meaning each host will have a separate value
    * Can also be called again in subsequent plays within a playbook

  ```yml
  - name: Check File
    hosts: all
    tasks:
    - shell: cat /etc/hosts
      register: result # this now registers the value of the shell as var named result
  ```

<h2>Defining Vars in Variable file</h2>
* Another option aside from defining vars within a playbook, is to define them within a variable file
* Suppose we are creating an ansible playbook and we want a list of vars that can be called from anywhere within that playbook, we'd defne a yaml file containing all of the vars

  ```yml
  http_port: 80
  https_port: 443
  ```

* All in all, whether we choose to define the vars in a variable file or within the inventory file, these variables will be called within a playbook as follows:

  ```yml
  {% raw %}
  port: '{{ http_port }}'
  # this is known as Jinja templating
  # standalone calls must be made within '', otherwise it is unnecessary
  # use indexing to call specific index from a list var
  {% endraw %}
  ```

<h2>Magic Vars</h2>
* Though some variables may exist outside of a specific host's scope, there are ways to retrieve vars from another host's scope
  - These are known as `Magic Variables`
* We have different types of `Magic Variables`
  - `hostvars[""]`: retrieves host scoped vars
  - `groups[""]`: retrieves hosts names within given groups
  - `group_names`: retrieves all groups the current host is apart of
  - `inventory_hostname`: retrieves name configured for the hosts in the inventory file

  ```yml
  {% raw %}
  # inventory
  web1
  web2 dns_server=10.5.5.4
  web3  

  # play
  - name: Print dns server
    hosts: all_servers
    tasks:
    - debug:
        msg: '{{ hostvars['web2'].dns_server }}'
        # this can also be writted as '{{ hostvars['web2']['dns_server'] }}'
        # ex: groups '{{ groups['all_servers'] }}
        # ansible facts, can also be retrieved this way
  {% endraw %}
  ```
