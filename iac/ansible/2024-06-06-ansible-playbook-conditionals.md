<h1>Ansible Playbook Conditionals</h1>
* `Ansible Playbooks` also allow you to define conditionals that can streamline the task process

<h2>Conditional Operators</h2>
* First we need to go over the list of operators that can be used in conditionals
  * `Comparsion Operators`: 
    - `==`: Equal to
    - `!=`: Not Equal to
    - `>`: Greater than
    - `<`: Less than
    - `>=`: Greater than or Equal to
    - `<=`: Less than or Equal to
  * `Logical Operators`:
    - `and`
    - `or`
    - `not`
  * `Membership Operators`:
    - `in`: checks if a value is present in a list, string, or map
    - `not in`: checks if a value is not present in a list, string, or map
  
<h2>When Conditionals</h2>
* We can specify that a task only run when a certain condition has been met

  ```yml
  ---
  - name: Install Apache
    hosts: web_servers
    gather_facts: true
    become: true
    tasks:
    - name: Install NGINX on Ubuntu
      when: ansible_os_family == "Debian" # calling var from ansible_facts
      # with when conditions, Jinja template braces are not needed, I'd include it for clarity for other users though
      apt:
        name: apache2
        state: present
    - name: Install NGINX on RedHat
      when: ansible_os_family == "RedHat" # calling var from ansible_facts 
      yum:
        name: httpd
        state: present
  ```

* `OR` and `AND` operators can also be used to expand on these conditions
  
  ```yml
  when: ansible_os_family == "Debian" and ansible_distribution_version == "16.04"
    # Note AND operators can be referenced as an array in when conditions
    when:
    - ansible_os_family == "Debian"
    - ansible_distribution_version == "16.04"
  when: ansible_os_family == "Debian" or  ansible_distribution_version == "16.04"
  ```
<h4>When Conditions & Ansible Facts</h4>
* You can also call `Ansible Facts` when using when conditionals in `Ansible`
  
  ```yml
  when: ansible_facts['os_family'] == 'Debian' and ansible_facts['distribution_major_version'] == '18'
  ```

<h4>When Conditions & Connection Vars</h4>
* Don't forget you can use connection vars defined in the inventory file, like `ansible_host`

  ```yml
  when: ansible_host == web1
  ```

<h2>Changed When Conditional</h2>
* `Ansible` allows you to define when a task has `changed` a remote host with the `change_when` conditional, based on the return of the output
  
  ```yml
  tasks:
  - name: Run Command
    ansible.builtin.shell: ls
    changed_when: True # will report changed since output will return true
    # can also be a list of conditions

  - name: use registers to report found file
    ansible.builtin.shell: ls -l | grep fileName
    register: result
    changed_when:
     - result.stdout.find('fileName') == 1
  ```


<h2>Registers</h2>
* `Registers` allow you to save the output of a task as a var that can be called later

  ```yml
  ---
  - name: Install Dependencies
    hosts: web_servers
    gather_facts: true
    become: true
    tasks: Check Apache Status
    - command: systemctl status httpd 
      register: status # saved command results as var,
    - mail:
        to: example@company.com
        subject: Service Down
        body: Httpd Service is Down
        when: status.stdout.find('down') != -1 # checked stdout of 'status' then did a search using 'find' method for the string and returns its position, if not found it returns '-1' which translates to false
  ```

<h2>Abort</h2>
* There may be cases where you want to abort the entire play on all hosts if a task fail or a certain percentage of hosts fail
  - `abort_errors_fatal`: allows you to define this at the playbook or the play level

  ```yml
  ---
  - hosts: all
    any_errors_fatal: true # if this tasks returns an error, ansible finishes the fatal task and stops all subsequent tasks
    # max_fail_percentage: # allows you to define the max percent of hosts in a patch that can fail before being aborted, you can set to 0 for no failures
  ```
