<h1>Ansible Vault</h1>

* `Ansible Vault` helps us store inventory data in an encrypted format 
  - `ansible-vault encrypt inventory`: encrypts the inventory file
  - You will be prompted to create a vault password
* Once an inventory file is encrypted, it cannot be used unless you pass in the password to decrypt the file
  - `ansible-playbook playbook.yaml -i inventory --ask-vault-pass`: prompts user to pass in vault password
  - `ansible-playbook playbook.yaml -i inventory --vault-password-file ~./vault_pass.txt`: passes password through file
  - `ansible-playbook playbook.yaml -i inventory --vault-password-file ~./vault_pass.py`: use python script that is capable of retrieving password, possibly from a third party resource like `Hashicorp Vault`
