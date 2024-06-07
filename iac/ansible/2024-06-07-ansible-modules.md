<h1>Ansible Modules</h1>
* `Ansible Modules` are categorized into different groups based on their functionality
  - `System`: actions to be performed at a system level
    * `User`
    * `Group`
    * `Hostname`
    * `Iptables`
    * `Lvg`
    * `Lvol`
    * `Make`
    * `Mount`
    * `Ping`
    * `Timezone`
    * `Systemd`
    * [Service](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/service_module.html): used to maintain services on a system, like start, stop, and restart
 
      ```yml 
      tasks:
      - name: Start Service
        ansible.builtin.service:
          name: httpd
          pattern: /usr/bin/executable # service control based on pattern matching on service
          state: started # you can use, reloaded, restarted, started, stopped
          # if service is being restarted, you can also add `sleep` field
      ```  
 
  - `Commands`: used to execute commands or scripts
    * [Command](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/command_module.html): executes a command on a remote host 
     
      ```yml
      tasks:
      - name: Execute Command
        # `command` can also be used
        ansible.builtin.command:
          cmd: cat file.txt
          # args: can be used to specify command and args similar to how it works in k8 deployment.yaml
          chdir: /home/ubuntu/test # changes dir before executing command
          creates: /home/ubuntu/file.txt # run command if this file doesn't exist on host , 'removes' can also be used too 
          # you can use this for if your command is creating or removing a file
      ```
  
    * `Expect`
    * `Raw`
    * [Script](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/script_module.html): used to run a local script from the ansible machine, on the remote host
 
      ```yml
      tasks:
      - name: Execute Script
        ansible.builtin.script: install-dependencies.sh 
        args:
          creates: file.txt # run script if this file does not exists in the host, removes can also be used
          # you can use this for if your command is creating or removing a file
      ```
 
    * `Shell`
  - `Files`: help work with files
    * `Acl`
    * `Archive`
    * `Copy`
    * `File`
    * `Find`
    * [Lineinfile](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/lineinfile_module.html): search for a line in a file and replace it or add it if it doesn't exists
    
      ```yml
      tasks:
      - name: Add line
        ansible.builtin.lineinfile:
          path: /etc/app/app.conf # file to be modified
      ``` 
 
    * `Replace`
    * `Stat`
    * `Template`
    * `Unarchive`
  - `Database`: help in working with databases
    * `MongoDB`
    * `Mssql`
    * `Mysql`
    * `Postgresql`
    * `Proxysql`
    * `Vertica`
  - `Cloud`: collection of modules from different cloud providers
    * `Amazon`
    * `Azure`
    * `Google`
    * `Digital Ocean`
    * `Linode`
    * `Docker`
  - `Windows`: helps you use `Ansible` in a windows environment
    * `Win_copy`
    * `Win_command`
    * `Win_domain`
    * `Win_file`
    * `Win_regedit`
    * `Win_shell`
    * `Win_service`
    * `Win_user`
