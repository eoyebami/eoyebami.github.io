<h1>Top Level Ansible Field</h1>
* Here I'd like to list all top level ansible fields that can be used in a playbook and their purposes

  ```yml
  name: # name of playbook
  hosts: # hosts this playbook will run against
  remote_user: # user ansible will connect to hosts as
  become: # allows permission escalation, default is sudo (bool)
  become_user: # sudo -su <user>
  any_errors_fatal: # any errors will cause immediate halt of playbook on all hosts, at that task (bool)
  ignore_errors: # ansible ignores failures (bool)
  gather_facts: # ansible gathers ansible facts at each hosts (bool)
  max_fail_percentage: # allows you to define the max percent of hosts in a patch that can fail before being aborted, you can set to 0 for no failures
  strategy: # defines strategy ansible uses to apply tasks (free/linear)
    # linear: ansible runs tasks in parallel and waits for each host to complete on each task before continuing on
    # free: each server runs independently of one another, ansible does not wait for each server to finish a task before moving on
  serial: # ansible uses linear strategy to batch servers (int)
    # for example. you can configure ansible to run playbook on 3 servers at a time
    # ansible has a default of max connections set to 5, defined by config 'forks = 5'
    # you can increase this as much as you like, provided you have enough resources for it
  ```
