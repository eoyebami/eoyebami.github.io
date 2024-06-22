<h1>Ansible Modules</h1>
* `Ansible Modules` are categorized into different groups based on their functionality
  - A list of support modules can be found [here](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/index.html)
* We have different types of modules
  - `ansible.builtin.*`: modules built into `ansible`
  - `ansible.posix`: part of posix collection and needs to be installed using `ansible-galaxy collection install ansible.posix`
  - `community.general`: part of the community general collection and needs to be installed using `ansible-galaxy collection install community.general`
  - `amazon.aws`: collection of aws specific modules and needs to be installed using `ansible-galaxy collection install amazon.aws`
  - `Import*`: modules that import specific resources into a playbook
    * [include tasks](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/include_tasks_module.html)
  
      ```yml
      tasks:
      - name Include task in play
        ansible.builtin.include_tasks:
          file: install.yaml
      ```
    
    * [import playbook](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/import_playbook_module.html#ansible-collections-ansible-builtin-import-playbook-module): cannot be used within a play
 
      ```yml
      tasks:
      - name: Import playbook
        ansible.builtin.import_playbook: mysql.yaml
        # vars: can also be passed to imported playbooks
      ```
 
    * [import role](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/import_role_module.html#ansible-collections-ansible-builtin-import-role-module): loads role, and allows you to control when tasks run
 
      ```yml
      tasks:
      - ansible.builtin.import_role:
          name: myrole

      - name: run tasks and pass role
        ansible.builtin.import_role:
          name: myrole
          tasks_from: main.yaml
        vars:
          http_port: 80
      ```
 
    * [include role](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/include_role_module.html#ansible-collections-ansible-builtin-include-role-module): load and execute a role
 
     ```yml
      tasks:
      # runs roles
      - ansible.builtin.include_role:
          name: myrole
      # runs role while passing var
      - name: run tasks and pass role
        ansible.builtin.include_role:
          name: myrole
          tasks_from: main.yaml
        vars:
          http_port: 80
      ```
  
  - `System`: actions to be performed at a system level
    * [apt](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/apt_module.html#ansible-collections-ansible-builtin-apt-module): manage apt packages
  
      ```yml
      tasks:
      - name:
        ansible.builtin.apt:
          name: http
          state: present
      ```
 
    * [Yum](https://docs.ansible.com/ansible/2.9/modules/yum_module.html): manage yum packages
    
      ```yml
      tasks:
      - name:
        yum:
          name: http
          state: present
      ```
 
    * [Add Host](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/add_host_module.html#ansible-collections-ansible-builtin-add-host-module): Add a host to the playbook in-mem inventory, can only be called later in the same play
 
      ```yml
      tasks:
      - name: Add host to a group
        ansible.builtin.add_hoset:
          name: 10.0.0.1 # host
          groups:
          - web_servers # will also create defined group if it doesn't already exist
          var1: value1 # define vars for host
     
      tasks:
      - name: Add hosts in more detailed
        ansible.builtin.add_host:
          hostname: web1
          groups:
          - web_servers
          ansible_host: 10.0.0.1
          ansible_port: 22
      ```
 
    * [Debug](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/debug_module.html#ansible-collections-ansible-builtin-debug-module): useful for debugging vars or expressions without stopping playbook
     
      ```yml
      tasks:
      - name: DEBUG
        ansible.builtin.debug:
          var: result # var defined by register in another play, will display
          # msg: "debug" # to display to a custom message
          verbosity: 2 # set verbose level for out
         
      ```
 
    * [Fail](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/fail_module.html#ansible-collections-ansible-builtin-fail-module): module fails the progress with a custom message
 
      ```yml
      tasks:
      - name: Fail
        ansible.builtin.fail:
          msg: Failure
      ```
 
    * [User](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/user_module.html): manager user accounts 
   
      ```yml
      tasks:
      - name: Add user
        ansible.builtin.user:
          name: eztest
          comment: Ezzie
          uid: 1001
          group: jenkins,wheel
          generate_ssh_key: yes
          ssh_key_bits: 2048
          ssh_key_file: .ssh/id_rsa
          create_home: yes
          # much more options like password, and expiry can be set
          # state can also be set to remove a user
      ```
 
    * [Group](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/group_module.html): manage groups on a host
 
      ```yml
      tasks:
      - name: Modify group
        ansible.builtin.group:
          name: docker
          state: present
          gid: 5000
      ```      
 
    * [Hostname](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/hostname_module.html): set system hostname
 
      ```yml
      tasks:
      - name: 
        ansible.builtin.hostname:
          name: web01
          use: systemd # method ansible should use to update hostname
      ```
 
    * [Systemd](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/systemd_service_module.html#ansible-collections-ansible-builtin-systemd-service-module): controle systemd units
 
      ```yml
      tasks:
      - name: Start httpd service
        ansible.builtin.systemd_service:
          name: httpd
          state: started # can also be reloaded, restarted and stopped
          daemon_reload: false # controls systemctl daemon-reload, can be used alone
          enabled: true # enable service
      ```
  
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
 
    * [Mount](https://docs.ansible.com/ansible/latest/collections/ansible/posix/mount_module.html): mounts disks
 
      ```yml
      tasks:
      - name: Mount disk
        ansible.posix.mount:
          path: /mnt/dvd # mount path
          src: /dev/xvda # partition for mount
          fstype: ext4 # filesystem type
          state: present # remount, unmount can also be used
      ```
 
    * [Timezone](https://docs.ansible.com/ansible/latest/collections/community/general/timezone_module.html): configures tz setting
     
      ```yml
      tasks:
      - name: Set TZ
        community.general.timezone:
          name: America/Denver
      ```
 
    * [Iptables](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/iptables_module.html): modify iptables
    * [Make](https://docs.ansible.com/ansible/latest/collections/community/general/make_module.html): run targes in a makefile
    * [Lvg](https://docs.ansible.com/ansible/latest/collections/community/general/lvg_module.html): creates, removes, and resizes logical volume groups
    * [Lvol](https://docs.ansible.com/ansible/latest/collections/community/general/lvol_module.html): creates, removes, and resizes logical volumes
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
  
    * [Expect](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/expect_module.html): executes a command and responds to prompts, host most have `python >=2.6` and `pexpect >= 3.3`, in order to function
      
      ```yml
      tasks:
      - name: Generate key pair
        become: true # set to true to activate privilege escalation, essentially like using sudo
        become_user: ec2-user # using sudo to switch to this user
        ansible.builtin.expect:
          command: ssh-keygen -t ed25519 -f .ssh/id_ed25519
          responses:
            "Enter passphrase (empty for no passphrase):": 
             - "\n"
            "Enter same passphrase again:": 
             - "\n"
      ```
 
    * [Script](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/script_module.html): used to run a local script from the ansible machine, on the remote host
 
      ```yml
      tasks:
      - name: Execute Script
        ansible.builtin.script: install-dependencies.sh 
        args:
          creates: file.txt # run script if this file does not exists in the host, removes can also be used
          # you can use this for if your command is creating or removing a file
      ```
 
    * [Shell](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/shell_module.html): exactly like `command` module, but runs the command through a shell on the remote node, so you can use operators like redirects and pipes
 
      ```yml
      tasks:
      - name: Execute Command
        ansible.builtin.shell:
          cmd: ls -l | grep '^#'
          args:
            executable: /bin/bash # specify a shell as an executable 
          chdir: /home/ubuntu/test # changes dir before executing command
          creates: /home/ubuntu/file.txt # run command if this file doesn't exist on host , 'removes' can also be used too
          # you can use this for if your command is creating or removing a file
      ```
   
    * [Raw](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/raw_module.html): similar to `shell` or `command`, but should not be used often
  - `Files`: help work with files
    * [Archive](https://docs.ansible.com/ansible/latest/collections/community/general/archive_module.html): creates or extends an archive
      
      ```yml
      tasks:
      - name: Archive files
        community.general.archive:
          path: /home/ubuntu/app-props/* # path on remote hosts, can be set as an array
          dest: /home/ubuntu/app-props.tgz # path on remote hosts
          # format: zip can set format
          exclude_path: /home/ubuntu/app-props/test.props # won't be archived, can be set as an array, wildcards can be used
          remove: true # removes regular file after compression
          force_archive: allows you to force archive even if only a single file is specified
      ```
  
    * [Unarchive](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/unarchive_module.html): unpacks and archive
 
      ```yml
      tasks:
      - name: Unarchive
        ansible.builtin.unarchive:
          src: app.tgz # grabs an archived file from the ansible host unarchives in remote
          dest: /etc/app/
          # remote_src: yes # unarchive file on remote host
          # keep_newer: true #  do not replace existing files, that are newer than the files in the archive
          # list_files" true # lists files in the arvhive
          # include, group, owner, mode, and exclude can also be used
      ```
 
    * [Copy](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/copy_module.html): copies files from ansible host to remote host
     
      ```yml
      tasks:
      - name: Copy file
        ansible.builtin.copy:
          src: /etc/hosts # path to file in ansible hosts
          dest: /etc/hosts # path to file in remote server
          # set ownership permissions
          owner: ec2-user 
          group: ec2-user
          mode: '0644'
          backup: yes # backups original if it diffs from copy
          force: true # transfers file if it doens't exist at dest
          # you can copy directory content by using '/' to let ansible know its a dir

      tasks:
      - name: Make duplicate of file on node 
        ansible.builtin.copy: 
          src: /etc/sudoers
          dest: /etc/sudoers.bak
          remote_src: yes
     
      tasks:
      - name: Incline copy
        ansible.builtin.copy:
          content: "# This is a README.md file"
          dest: /etc/app/README.md
      ```
 
    * [File](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/file_module.html): controls files, dirs, symlinks 
 
      ```yml
      tasks:
      - name: Modify File attributes
        ansible.builtin.file:
          path: /etc/app/app.conf
          owner: root
          group: root
          mode: '0644'
      
      tasks:
      - name: Create a symlink
        ansible.builtin.file:
          src: /home/ubuntu/app/etc/app.conf
          dest: /etc/app/app.conf
          owner: root
          group: root
          mode: '0644'
          state: link # symbolic link creation, 'hard' will create a hard link

      tasks: 
      - name: Create file
        ansible.builtin.file:
          path: /etc/app/app.conf
          state: touch # creates a file, if it already exists, nothing will happen
          mode: '0644' # modifies permissions on file if it already exists
          modification_time: preserve # will preserve modification time of file, this is default for all states except touch
          # touch sets modification_time to now
          access_time: preserve # functions the same way modification_time does

      tasks: 
      - name: Create a dir
        ansible.builtin.file:
          path: /etc/app
          state: directory
          recurse: true
          mode: '0755'

      tasks:
      - name: Remove file
        ansible.builtin.file:
          path: /etc/app/app.conf
          state: absent # can be used to recursively delete dirs also
      ```
 
    * [Find](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/find_module.html): returns a list of files based on criteria, essentially the find command in bash
      
      ```yml
      tasks:
      - name: Find all tgz files
        ansible.builtin.find:
          path: /opt/app
          patterns: 
          - '*.tgz' # regex can be used, can be defined above too
          use_regex: yes 
          recurse: yes # recursive search
          depth: 4 # max depth to search
          age: 1w # select files which age is equal to or greater than the time
          follow: true # will follow symlinks
          hidden: true # will include hidden files
          mode: '0644' # select files with matching perms
          size: 10m # select files whose size is equal to or greater than specified
          file_type: file # can be link, any, or directory
          excludes: 
      ```
 
    * [Lineinfile](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/lineinfile_module.html): search for a line in a file and replace it or add it if it doesn't exists
    
      ```yml
      # use regex to find line and replace it
      tasks:
      - name: Replace line
        ansible.builtin.lineinfile:
          path: /etc/app/app.conf # file to be modified
          regexp: '^SELINUX=' # finds line that starts with 'SELINUX='
          line: SELINUX=enforcing
          backup: true # creates a backup file including the timestamd info

      # remove a line from a file
      tasks:
      - name: Remove line
        ansible.builtin.lineinfile:
          path: /etc/sudoers
          state: absent # present can also be used, can be used with line to add or remove
          regexp: '^wheel' # finds line that starts with 'wheel'
   
      # modify a line and set file permissions
      tasks: 
      - name: Replace localhost entry
        ansible.builtin.lineinfile:
          path: /etc/hosts
          regexp: '^127\.0\.0\.1' # regex requires escaping of periods
          # search_string: '127.0.0.1' # can use this to avoid escaping, searches literal strings
          line: 127.0.0.1 localhost
          owner: root # set user ownership
          group: root # set group ownsership
          mode: '0644' # set perms
          insertafter: '^# localhost' # insert line after matching specified regex
          # if both regex and insertafter are specified, insertafter will only be honored if no matching regex is found
          # insertbefore can also be used
      ``` 
 
    * [Replace](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/replace_module.html): Similar to `lineinfile`, but this replaces all instances of a pattern within a file
 
      ```yml
      tasks:
      - name: Replace pattern
        ansible.builtin.replace: 
          path: /etc/ssh/sshd_config # path to file
          regex: '^#PasswordAuthentication' # replaces all occurrences of this within a file
          replace: 'PasswordAuthentication yes'

      tasks:
      - name: Replace pattern
        ansible.builtin.replace:
          path: /etc/ssh/sshd_config # path to file
          after: '^#Example Config' # start replacing after this expression  
          before: '^# EOF' # stop replacing before this expression
          regex: '^#PasswordAuthentication' # replaces all occurrences of this within a file
          replace: 'PasswordAuthentication yes'
      ```
 
    * [Template](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/template_module.html): takes template files and parses them using jinja templates, before copying to remote host
 
      ```yml
      tasks:
      - name: Template a property file
        ansible.builtin.template:
          src: /etc/applications.properties.j2 # Jinja template file
          dest: /home/ec2-user/app/etc/applications.properties 
          owner: ec2-user
          group: ec2-user
          mode: '0644'
          backup: true # creates backup of file 
          force: true # if file already exists do nothing, if false then copy over
      ```
 
    * [Stat](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/stat_module.html): gets the stats of a file, all possible stats can be found [here](https://docs.ansible.com/ansible/2.9/modules/stat_module.html?highlight=stat)
 
      ```yml
      tasks:
      - name: Get stat of a file
        ansible.builtin.stat:
          path: /etc/fileName
        register: stat # save output of file to var
      - name: Debug
        ansible.builtin.fail:
          msg: "Failure"
        when: stat.stat.pw_name != 'root'
      ```
 
    * [Acl](https://docs.ansible.com/ansible/latest/collections/ansible/posix/acl_module.html): set and retrieve acls information
  - `Cloud`: collection of modules from different cloud providers
    * [Amazon](https://docs.ansible.com/ansible/latest/collections/amazon/aws/index.html): focus will be on aws in particular, since its my major use case. Its possible to create, stop, and modify instances with this module. I'd like to explore using this to spin up hosts and then adding these hosts using the `add_hosts` module to then set up app configurations on.
    * [ec2 instance info](https://docs.ansible.com/ansible/latest/collections/amazon/aws/ec2_instance_info_module.html#ansible-collections-amazon-aws-ec2-instance-info-module): gathers info on ec2 instances
    * [ec2 instance](https://docs.ansible.com/ansible/latest/collections/amazon/aws/ec2_instance_module.html#ansible-collections-amazon-aws-ec2-instance-module): creates and manages aws ec2 instances
    * [ec2 tag module](https://docs.ansible.com/ansible/latest/collections/amazon/aws/ec2_tag_module.html#ansible-collections-amazon-aws-ec2-tag-module): creates and removes tags on ec2 resources
    * [ec2 key info](https://docs.ansible.com/ansible/latest/collections/amazon/aws/ec2_key_info_module.html#ansible-collections-amazon-aws-ec2-key-info-module): gathers info on preexisting aws keys, that I assume can be called later
    * [ec2 security group info](https://docs.ansible.com/ansible/latest/collections/amazon/aws/ec2_security_group_info_module.html#ansible-collections-amazon-aws-ec2-security-group-info-module): gathers info on security groups, that I assume can be called later
    * [ec2 vpc net info](https://docs.ansible.com/ansible/latest/collections/amazon/aws/ec2_vpc_net_info_module.html#ansible-collections-amazon-aws-ec2-vpc-net-info-module): gathers info on vpcs, that I assume can be called later
    * [ec2 cpv subnet info](https://docs.ansible.com/ansible/latest/collections/amazon/aws/ec2_vpc_subnet_info_module.html#ansible-collections-amazon-aws-ec2-vpc-subnet-info-module): gathers info on subnets, that I assume can be called later 
    * [rds cluster info](https://docs.ansible.com/ansible/latest/collections/amazon/aws/rds_cluster_info_module.html#ansible-collections-amazon-aws-rds-cluster-info-module): gathers info on rds, that I assume can be called later
    * [rds](https://docs.ansible.com/ansible/2.10/collections/community/aws/rds_module.html): rds module that allows more freedom on what to do when manipulating aws rds
