<h1>Ansible Collections</h1>

* `Ansible Collections` allow you to access vendor specific content like plugins, modules, roles, etc.
  - A collection encapsulates all of these resources
* We have a bunch of built in modules that we call, but there are modules in the `posix` collection as well
  - All the CSPs also have their own modules you can call
  - You simply need to install them using the `ansible-galaxy collection install <collection-name>` command

* We have different types of modules
  - `ansible.builtin.*`: modules built into `ansible`
  - `ansible.posix`: part of posix collection and needs to be installed using `ansible-galaxy collection install ansible.posix`
  - `community.general`: part of the community general collection and needs to be installed using `ansible-galaxy collection install community.general`
  - `amazon.aws`: collection of aws specific modules and needs to be installed using `ansible-galaxy collection install amazon.aws`

* There are multiple benefits found in using `Collections`
  1. `Enhanced Functionality`: much more flexibility when creating playbooks
  2. `Modularity and Resuability`: you can create you own custom collection and reference them in playbooks
  3. `Simplified Distribution and Management`: easily define collections in a `requirements.yml`

<h2>Calling Collections in Playbooks</h2>

* Once you've installed a collection, you need to reference it similarly to how you'd reference a role within a playbook

  ```yml
  ---
  - hosts: localhost
    collections:
    - amazon.aws # calling the amazon aws collection

    tasks:
    - name: Create an S3 bucket
      aws_s3_bucket:
        name: test-bucket
        region: us-west-1
  ```

<h2>Managing Collections</h2>

* You can manage collections to a playbooks by defining a `requirements.yml` file containing the collections you want and their versions 

  ```yml
  --- 
  collections:
  - name: amazon.aws
    version: "1.5.0"
  ```

* You can then install all collections within the yaml file by running `ansible-galaxy collection install -r requirements.yml`
