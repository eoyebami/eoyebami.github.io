<h1>Ansible File Separation</h1>

* When structuring your `Ansible` root directory, there's several things to keep in mind

<h2>Inventory: Separating Host Variables</h2>

* It can be a bit messy to store all of your host vars in the `inventory` file, so separating them into there own directory is a lot cleaner

* Create a directory called `host_vars` and within this directory create separate yaml files for each host (this yaml file should share the same name as the host in the `inventory`)

  ```yml
  # inventory 
  [web_servers]
  web1

  # host_vars/web1.yaml
  ansible_host: 10.0.0.100
  dns_server: 10.1.1.100
  # ansible autmatically reads these yaml files and associates them with the hosts in the inventory file
  ```

<h2>Inventory: Separating Group Variables</h2>

* Beyond just abstracting host vars, you can also abstract group vars into a directory called `group_vars`

  ```yml 
  # group_vars/web_servers.yml
  # This should also be named after the group in the inventory file
  dns_serversL 10.1.1.100
  ```

<h3>Centralizing inventory related files</h3>

* You can centralize all inventory related files and directories into a single directory called `inventory` within which you would keep:
  - `inventory` ini file
  - `host_var` dir
  - `group_var` dire

<h3>Pulling in external Vars</h3>

* Now if we want to add external vars to our playbook we would use the `include_vars` module to do so

  ```yml
  {% raw %}
  tasks:
  - include_vars:
      file: /path/to/external/file
      name: file # all vars in this file will be loaded into this var "file"
      # you can then call each var from the file using jinja2 templating (i.e. {{ file.dns_server }})
  {% endraw %}
  ```

<h2>Tasks: Separation</h2>

* Similarily to how we separate inventory related content, we can also separate tasks into a subdirectory called `tasks`

  ```yml
  # tasks/db.yaml
  - name: Install MySQL
    yum:
      name: mysql
      state: present
  ```

* You can the import these tasks into your playbook

  ```yml
  tasks:
  - include_tasks: tasks/db.yaml
  ```

* Its almost like you're defining functions that can be reused over and over again
* NOTE: Conditionals can also be used when including tasks
