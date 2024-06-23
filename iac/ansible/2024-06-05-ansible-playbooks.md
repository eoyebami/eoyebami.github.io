<h1>Ansible Playbooks</h1>

* In `Ansible Playbooks` we define what we want `Ansible` to do to the servers that are defined in the inventory
* All `Playbooks` are written in YAML and there are differen components to a `playbook`
  - A `playbook` is a single YAML file containing a set of plays
  - A `play` is a set of tasks that are to be run on the hosts
  - A `task` is an action to be performed on the host

<h2>Playbook Format</h2>

* The `Playbook` is an array of maps of each play that should be run
  
  ```yml
  # playbook.yml
  --- # all ansible playbooks must start with a triple hyphen
  - name: Test Play # denotes first play
    hosts: web-servers # which hosts in the inventory file, this play should execute one
    remote_user: root # what remote user ansible should connect as to perform plays
    
    tasks: # list of tasks associated with this play
    - name: Execute 'ls' command
      command: ls # using the command module to run ls on the hosts
  ```

* Once a `playbook` has been created you simply need to run `ansible-playbook <playbook.yml>` to execute it

<h2>Verifying Playbooks</h2>

* There are different modes we can use when verifying our `playbooks`
  - `Check Mode`: Running a dry-run against your `playbook` where `Ansible` doesn't actually execute anything
    * This allows you to preview the playbook changes without applying them
    * `ansible-playbook playbook.yml --check`
    * Not all `Ansible Modules` support `check mode` and tasks that contain such modules will be skipped during the dry-run
  - `Diff Mode`: When used with `check mode` shows the differences between the current state of the hosts and the state after execution
    * `ansible-playbook playbook.yml --check --diff`
  - `Syntax Check`: ensures playbook syntax has no errors
    * `ansible-playbook playbook.yml --syntax--check`: will display error message if any

<h2>Linting Playbooks</h2>

* `Ansible Lint` checks code for potential errors, bugs, stylistic errors, etc
   - `ansible-lint playbook.yml`: will display errors and warnings about potential issues within the playbook and suggests fixes for such issues
