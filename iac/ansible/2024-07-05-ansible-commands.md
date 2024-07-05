<h1>Ansible Commands to Remember</h1>

```yml
ANSIBLE-PLAYBOOK
ansible-playbook -i inventory playbook.yaml # installs playbook with '-i' flag specifyinh inventory file
ansible-playbook -i inventory playbook.yaml --check # runs ansible playbook in dry-run mode
ansible-playbook playbook.yaml --start-at-task "Start Httpd service" # specify a task where this playbook should start
ansible-playbook playbook.yaml --tags "install" # by tagging tasks within your playbook, you can specify that these specific tasks run
ansible-playbook playbook.yaml --skip-tags "install" # skip tags with this tag
ansible-playbook playbook.yaml --become-method sudo --become-user root # method used to become a user
ansible-playbook playbook.yaml --force-handlers # forces handlers to run even if tasks fail
ansible-playbook playbook.yaml --syntax-check # performs syntax check on playbook
ansible-playbook playbook.yaml --step # confirms each task before running
ansible-playbook playbook.yaml --timeout 3600 # timeout in seconds  
ansible-playbook playbook.yaml --extra-vars "key=value" # pass extra vars, YAML and JSON also accepted, pass files using '@'
ansible-playbook playbook.yaml --user ec2-user # connect as user
ansible-playbook playbook.yaml --verbose # runs in verbose mode, can denote -v

ANSIBLE-CONFIG
ansible-config list # lists available configs
ansible-config list --config ansible.cfg # specify config file 
ansible-config list --format yaml # outputs in a specific format, can be JSON too
ansible-config list --format yaml --type string # filters by type
ansible-config dump # shows current settings and merges with ansible.cfg if specified
ansible-config dump --format json # shows settings and formats in json
ansible-config dump --only-changed # shows only configs that differ from default
ansible-config dump --config ansible.cfg # shows settings of specific conf file
ansible-config dump --type string # filters by type
ansible-config view --config ansible.cfg --type string # displays current config file and filters by type
ansible-config init # create initial config file
ansible-config init --format yaml # output format for init 
ansible-config init --config # creates initial config in specified location
ansible-config init --type string # creates initial config with filter types
ansible-config init --type all --disabled # creates initial config with commented values

ANSIBLE-DOC
ansible-doc ping # displays info on ping module
ansible-doc --metadata-dump yum # dumps json metadata for all entries
ansible-doc --metadata-dump --no-fail-on-errors apt # dumps metadata and will not fail on error, JSON output
ansible-doc --list # list available pluginsans
ansible-doc --type module become # specify type to display

ANSIBLE-GALAXY
ansible-galaxy # performs role and collection related operations
ansible-galaxy role list # lists all roles currently on system
ansible-galaxy role init mysql # initializes a role dir called mysql
ansible-galaxy role install geerlingguy.mysql # installs defined role
ansible-galaxy role install geerlingguy.mysql --init-path ./roles # installs defined role, in specified path
ansible-galaxy role install geerlingguy.mysql --force # force overwrite 
ansible-galaxy role remove geerlingguy.mysql # removes role
ansible-galaxy role search --author test --galaxy-tags tag # searches for role on ansible-galaxy server
ansible-galaxy role import # imports a role into ansible galaxy
ansible-galaxy collection download community.general # downloads collection and dependencies as tar
ansible-galaxy collection download --no-deps -r requirements.yaml # downloads collection and no collections, as well as defining file path containing collection list
ansible-galaxy collection install community.general # installs collection
ansible-galaxy collection install -r requirements.yaml --force-with-deps --upgrade # installs collection, upgrades installed collection artifacts and forces overwrites with dependecies
ansible-galaxy collection list # lists all collections ro roles

ANSIBLE-INVENTORY
ansible-inventory -i inventory.yaml # displays config inventory
ansible-inventory -i inventory.yaml --host web1 # displays specific host infor
ansible-inventory -i inventory.yaml --list # lists all host info
ansible-inventory --output inventory.yaml # outputs to a specified path
ansible-inventory -i inventory.yaml --yaml # outputs in yaml, instead of default json format
```
