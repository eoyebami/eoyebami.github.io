<h1>Ansible Roles</h1>

* `Ansible Roles` enable us to reuse and share Ansible code efficiently, in short, its basically a package manager for ansible playbooks meant to fulfil a specific tasks like installing mysql


<h2>Roles: Directory Structure</h2>

* `Roles` have a defined directory structure with 7 main standard directories
 
  ```
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
 
