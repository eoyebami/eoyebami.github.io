<h1>Ansible Handlers</h1>

* `Ansible Handlers` are tasks associated with other tasks as dependencies, that only get triggered if the corresponding tasks make a change
  - `Handlers` are triggered by tasks, events, and notifications
* Ex:

  ```yml
  ---
  name: Verify HTTPD
  hosts: webservers
  tasks:
  - name: Write Apache file
    ansible.builtin.copy: 
      src: index.html
      dest: /var/www/html/index.html
    notify: # notifies list of handlers defined
    - Restart Apache
  handlers: # this handler will run only when called
  - name: Restart Apache
    ansible.builtin.server:
      name: httpd
      state: restarted
  ```

<h2>Force Handler Run</h2>

* By default `Handlers` run when all tasks in the play have completed, this is so when multiple tasks notify a `handler` then that handler only needs to run once
* If you need `handlers` to run earlier you need to include a task to flush handlers

  ```yml
  tasks:
  - name: Flush Handlers
    meta: flush_handlers # triggers any handlers that have been notified at that point in play
  ```

* Once the handlers are run, they can be notified and run again in later sections of the play

<h2>Change_When and Handlers</h2>

* You can use the `changed_when` conditional to control whether or not a task is reported as changed and will notify a handler

  ```yml
  tasks: 
  - name: Copy http configuration
    ansible.builtin.copy:
      src: httpd.conf
      dest: /etc/httpd/conf/httpd.conf
    changed_when: True # since this always runs, it will always report true
    notify: Restart Apache #  reported True, will notify handler
  handlers: # this handler will run only when called
  - name: Restart Apache
    ansible.builtin.server:
      name: httpd
      state: restarted
  ```

