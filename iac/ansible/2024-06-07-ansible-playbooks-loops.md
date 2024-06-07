<h1>Ansible Playbook Loops</h1>
* `Ansible Playbooks` also allow you to define loops to iterate of lists
* There are 3 types of loop directives that can be called in a `playbook`:
  - `loop`
  - `with_*`
  - `until`

<h2>Loop Directive</h2>
* You can use loop conditions to iterate over any list you have defined

  ```yml
  ---
  - name: Install Dependencies
    hosts: web_servers
    gather_facts: true
    become: true
    vars:
      dependencies: # list of dependencies defined
      - name: jq
        required: True
      - name: curl
        required: True
      - name: zip
        required: False
    tasks:
    - name: Install "{{ item.name }}" # iterating over 'name' object within dependencies
      when: item.required == True  # iterating over 'required' object within dependencies
      loop: "{{ dependencies }}" # loops defines dependencies var as 'item', 'item' is the var each item is the loop will be saved under, always
      apt:
        name: "{{ item.name }}" # iterating over 'name' object within dependencies
        state: present
  ```
<h4>Loop Control</h4>
* Control the how the loop functions 

  ```yml
  ---
  - name: Install Dependencies
    hosts: web_servers
    gather_facts: true
    become: true
    tasks:
    - name: Echo "{{ item.name }}" # iterating over 'name' object within dependencies
      command: echo "{{ item.name }}
      # no_log: true # deactivates console output for loop
      loop:
      - name: web1
        disk: 3gb
      loop_control:
        label: "{{ item.name }}" # only name will be displayed in console output
        # no_log: true # deactivates console output for loop
        pause: 3 # how long ansible should wait before iterating over the next item in the array
        index_var: index # a var you can specify to keep track of where you are index wise, within this loop
  ```

<h2>With_* Directive</h2>
* You can consider this an older version of the `loop` directive, since it functions almost exactly the same way
* There are multiple types of `with` directives that can be used
  - `with_items`
  - `with_files`
  - `with_url`
  - `with_dict`
  - `with_env`
  - `with_k8s`
  - `with_inventory_hostnames`
  *  There are many more, but these are a few I'd like to list

  ```yml
  ---
  - name: Install Dependencies
    hosts: web_servers
    gather_facts: true
    become: true
    vars:
      dependencies: # list of dependencies defined
      - name: jq
        required: True
      - name: curl
        required: True
      - name: zip
        required: False
    tasks:
    - name: Install "{{ item.name }}" # iterating over 'name' object within dependencies
      when: item.required == True  # iterating over 'required' object within dependencies
      with_items: "{{ dependencies }}" 
      apt:
        name: "{{ item.name }}" # iterating over 'name' object within dependencies
        state: present
  ```

<h4>With_*: iterate over a dict</h4>

  ```yml
  ---
  - name: Install Dependencies
    hosts: web_servers
    gather_facts: true
    become: true
    vars:
      users:
        alice:
          uid: 1001
        ben:
          uid: 1002
    tasks:
    - name: Create User "{{ user.key }}"
      user:
        name: "{{ user.key }}" # calling the "key" field
        uid: "{{ user.value.uid }}" # calling the "uid" object from the "values" field
        # don't really see the point in this, and array would do the same
      with_dict: "{{ users }}" # iterating over the "users" key, 
  ``` 

<h2>Until Loop</h2>
* `Until` loops can be used to retry a task until a condition is met
 
  ```yml
  ---
  - name: Verify Logs 
    hosts: nodes
    become: true
    tasks:
    - shell: "cat /var/log/app/app.log | grep -c "Ready""
      register: status
      until: status.stdout | int == 4 # run this task until the string "Ready" appears 5x in the log file 
      retries: 5 # retry 5x 
      delay: 5 # wait 5s between each retry
     # this can also be used with the with_* and loop directive
  ```
