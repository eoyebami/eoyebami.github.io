<h1>Ansible Roles</h1>

* `Ansible Roles` enable us to reuse and share Ansible code efficiently, in short, its basically a package manager for ansible playbooks meant to fulfil a specific tasks like installing mysql


<h2>Roles: Directory Structure</h2>

* `Roles` have a defined directory structure with 7 main standard directories
 
  ```yml
  roles/
    common/ # root dir for role
      tasks/ # task files for role
        main.yaml # list of tasks that this role provides to the play in execution
      handlers/ # handles for this role
        main.yaml # list of handlers that are imported into the parent play for use
      templates/ # jinja templates for this role
        fileName.j2 # contains jinja2 templates
      files/ # files for this role
        fileName.txt # contains files that are available for use by role
      vars/ # vars for this role
        main.yaml # high priority vars provided by role
      defaults/ # def vars for this role
        main.yaml # low priority vars provided by role
      meta/ # metadata for role, including role dependencies and galaxy metadata
  ```

* By default `Ansible` will look for a `main.yaml` file when executing a role, if you want to define other tasks, then you'll need to `import_tasks` within the `main.yaml`
  - Another option can be to just call task directly when loading the role
 
    ```yml
    - name: Call specific tasks from role
      include_role:
        name: mysql
        tasks_from: apt.yaml # call this specific task from the role
    ```

* You can install precreated roles or upload your own roles to this ansible library called [ansible galaxy](https://galaxy.ansible.com/ui/)

<h2>Create Ansible Role</h2>
* `Ansible Galaxy` allows you to create a role template on-demand, fit with all the directories necessary for a role
  - `ansible-galaxy init mysql`: initializes a role directory called mysql
* You can upload your role via a github repo containing the yamls files
* To use a role, first go through the [ansible galaxy](https://galaxy.ansible.com/ui/) and find the role you need
  - `ansible-galaxy install geerlingguy.mysql`: which will install the role into the default ansible role directory `/etc/ansible/roles`
  - `ansible-galaxy install gerrlingguy.mysql -p ./roles`: downloads to a specific directory
  - Which can then be called within your playbook
* Extra Galaxy Commands:
  - `ansible-galaxy list`: displays all roles currently on your system
  - `ansible-galaxy dump`: dump list of configs for ansibe config

<h2>Import Roles</h2>
* These roles can also be called within a playbook
  - Assume we have within our directory a separate subdirectory called `roles` that includes all the roles we'd like to include in our playbook
  - We'd simply call it within our playbook 
    
    ```yml
    # Play
    - name: Grab roles
      hosts: all
      roles:
      - mysql # role found within our roles/
      # additionals options can also be passed when calling a role
      - role: roleName
        become: yes # activate escalated permissions
        vars:
          user: user01
    ```

* Roles can also be defined in the default location specified within the `/etc/ansible/ansible.cfg`
  - The default location being `/etc/ansible/roles`
  - This default location can be changed within the cfg file by setting `roles_path = /path/to/roles`
 
  

