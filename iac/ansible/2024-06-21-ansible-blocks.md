<h1>Ansible Blocks</h1>

* `Ansible Blocks` create a logical group of tasks, its similar to the post stage within a Jenkinsfile
  - `block`: You can define a group of tasks that run
  - `rescue`:  You can define another set of tasks that run if the block group fails
  - `always`: You can deine a set of tasks that always run

  ```yml
  tasks:
  - name: Start Application
    # these blocks can also call notify handlers
    # remember if you need them to run immediatly flush_handlers
    block: # define a group of tasks
    - name: Copy Configs
      ansible.builtin.copy:
        src: app/
        dest: /home/ec2-user/
    
    - name: Confirm copy
      ansible.builtin.command:
        cmd: cat /home/ec2-user/app/app.py
    rescue: 
    - name: Create file
      ansible.builtin.command:
        cmd: touch app.py
        creates: /home/ec2-user/app
        chdir: /home/ec2-user/
    always:
    - name: delete file
      ansible.builtin.file:
        path: /home/ec2-user/app
        state: absent 
  ```

* `Ansible Blocks` allow you to negate repetition, by containing a a subset of tasks into a single block
  - Where alls vars, conditionals, will apply to all tasks within that block

  ```yml
  tasks:
  - name: Start Application
    # these blocks can also call notify handlers
    # remember if you need them to run immediatly flush_handlers
    block: # define a group of tasks
    when: ansible_facts['distribution'] == 'CentOS'
    become: true
    ignore_errors: true
    # ignore errors on a task, proceeds even at failure
    - name: Copy Configs
      ansible.builtin.copy:
        src: app/
        dest: /home/ec2-user/

    - name: Confirm copy
      ansible.builtin.command:
        cmd: cat /home/ec2-user/app/app.py
  ``` 
