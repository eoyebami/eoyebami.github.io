<h1>Ansible Jinja2 Templating</h1>

* `Ansible` parses all playbooks with `Jinja2` templates before executing them
  - Essentially a parsed version of your playbook is what is run
* Now with spare templates in your directory, you can also include `Jinja2` templating
  - You would append each file with a `*.j2`
  - To have `Ansible` parse these template files before copying them over to the destination you would use the template module 
 
    ```yml
    tasks:
    - name: Copy index.html to remote
      template:
        src: index.html.j2 # template file
        dest: /var/www/html/index.html # Ansible parses these files before copying to destination
    ```

* Template files can be placed in their own subdirectories, and they will automatically be picked up by `Ansible` when called, so full path will not need to be specified

* You can even call magic vars within these template files as well

  ```yml
  {% raw %}
  {% for node in groups['web_servers'] %}
  {{ hostvars[node].ansible_host }} {{ node }} # save ip and hostname  of node to /etc/hosts files
  {{ hostvars[node].inventory_hostname }} {{ hostvars[node].ansible_facts.distribution }} # ansible facts can also be used
  {% endfor %}
  {% endraw %}
  ```

<h2>Ansible Jinja2 Filters</h2>

* We've covered some useful Jinja2 filters we can use, lets go over some `ansible-specific` filters that can be used
  - More notes on Jinja can be found [here](https://eoyebami.github.io/iac/ansible/2024-06-26-jinja2-templates.html)
* Filters allow you to transform and manipulate data on the controlnode before being executed on the host node

* `Handling Undefined Vars`: the `default` filter has a couple uses

  ```yml
  {% raw %}
  # define default vars
  {{ some_var | default('here') }}

  # use default when var is false or empty string
  {{ some_var | default('here', true) }}

  # make var optional, omit field
  {{ some_var | default(omit) }}
  {% endraw %}
  ```

* `Mandory Vars`: Vars that must be defined
  
  ```yml
  {% raw %}
  {{ some_var | mandatory }}
  {% endraw %}
  ```

* `Tenary Operations`: sets values for true/false/null

  ```yml
  {% raw %}
  {{ some_var | ternary('stopped', 'restarted', 'started') }}

  # using operators
  {{ (some_var == 'above_threshold')  | ternary('stopped', 'started', 'restarted') }}
  {% endraw %}
  ```

* `Manipulating Data Types`

  ```yml
  {% raw %}
  # get var type
  {{ some_var | type_debug }}

  # convert maps to lists
  {{ mydict | dict2items }}
  ex:
  tags:
    app: pay
  
  - key: app
    value: payment

  # configure key/val fields
  {{ mydict | dict2items(key_name="type", value_name='action') }}
  ex: 
  - type: app
    action: payment

  # convert lists to maps
  {{ mylist | items2dict }}
  tags:
  - key: app
    value: payment

  app: payment

  # configure key/val fields
  {{ mylist | items2dict(key_name='type', value_name='action') }}
  tags:
  - type: app
    actions: payment
  
  app: payment

  # convert to bool
  {{ some_var | bool }}
  
  # convert to int
  {{ some_var | int }}

  # convert to string
  {{ some_var | str }}
  {% endraw %}
  ```

* `Data Formatting`: between YAMLs and JSONs

  ```yml
  {% raw %}
  # convert yaml to json
  {{ some_var | to_json }}
  
  # convert json to yaml
  {{ some_var | to_yaml }}

  # convert to human readable json
  {{ some_var | to_nice_json }}

  # convert to human readable yaml
  {{ some_var | to_nice_yaml }}

  # modify indentation and width
  {{ some_var | to_json(indent=2, width=1000) }}
  {{ some_var | to_yaml(indent=2, width=1000) }}
  # works with nice filters as well

  # read in already formatted data
  {{ some_var | from_json }}
  {{ some_var | from_yaml }}

  # read multi doc yaml strings
  {{ some_var | from_yaml_all | list }}
  # converts each item to a list
  {% endraw %}
  ```

* `Combining Data`

  ```yml
  {% raw %}
  # combine items from multiple lists
  {{ [1,2,3] | zip(['a', 'b', 'c']) | list }} # zips together and converts to list

  # provides shortest combo of lists
  {{ [1,2,3] | zip(['a', 'b', 'c', 'd']) | list }} # zips together and converts to list

  # provides longest combo of lists
  {{ [1,2,3] | zip_longest(['a', 'b', 'c', 'd'], fillvalue='null') | list }} # zips together and converts to list
  # fill value uses specified place holder for any missing combos

  # construct dict from 2 lists
  stdin:
    app_list:
    - uno
    - dos
    type_list:
    - env
    - name
  {{ mydict(app_list | zip(type_list)) }}
  stdout:
    uno: env
    dos: name

  # filter for unique values in a list
  {{ mylist | unique }}
 
  # combine 2 lists, no duplicates
  {{ list1 | union(list2) }}

  # grab values that exist in both lists
  {{ list1 | intersect(list2) }}

  # grab items that exist in one list and not the other
  {{ list1 | difference(list2)

  # grab items exclusive to both lists
  {{ list1 | symmetric_difference(list2) }}
  {% endraw %}
  ```

* `JSON Querying`: using JSONPATH

  ```yml
  {% raw %}
  {{ some_var | community.general.json_query('metadata.name') }}

  # queries can even be called from vars
  vars:
    query: "metadata[?cluster='cluster1'].port"
  {{ some_var | community.general.json_query(query) }}

  # print out in comma seperated string
  {{ some_var | community.general.json_query('items[*].metadata.name') | join(',') }}"

  # single quote escaping 
  {{ some_var | community.general.json_query('metadata[?cluster=''cluster1''].port') }}

  # print multiple objects
  vars:
    query: "metadata[?cluster='cluster1'].{name: name, port: port}"
  {{ some_var | community.general.json_query(query) }}

  # use with yaml 
  {{ some_var | to_json | from_json | community.general.json_query('metadata.name') }}

  # contain function
  vars:
    query: "metadata[?contains(name, 'cluster1')].port"
  {{ some_var | community.general.json_query(query) }}
  {% endraw %}
  ```

* `IpAddress Filters`
  
  ```yml
  {% raw %}
  # validate ip address
  # requires collection install
  # ansible-galaxy collection install ansible.utils
  {{ some_var | ansible.utils.ipaddr }}
  {{ some_var | ansible.utils.ipv4 }}
  {{ some_var | ansible.utils.ipv6 }}

  # retrieve ip from cidr
  {{ some_var | ansible.utils.ipaddr('address') }}
  {{ some_var | ansible.utils.ipv4('address') }}
  {{ some_var | ansible.utils.ipv6('address') }}

  # check if ip is accessible public/private
  {{ some_var | ansible.utils.ipaddr('public') }}
  {{ some_var | ansible.utils.ipaddr('private') }}

  # confirm if ip is in a specific range
  {{ some_var | ansible.utils.ipaddr('10.0.0.0/8') }}
  {% endraw %} 
  ```

* `Hashing and Encryption`
  
  ```yml
  {% raw %}
  # get sha1 of string
  {{ 'test' | hash('sha1') }}
  {% endraw %}
  ```

* `Regex`

  ```yml
  {% raw %}
  # filter for line that starts with World, no casesensitivity
  {{ 'Hello\nWorld' | regex_search('^world', multiline=True, ignorecase=True) }}
  {{ 'Hello\nWorld' | regex_search('(?im)^world') }}

  # extract all occurrences of search
  {{ 'Hello\nWorld\nhello\nworld' | regex_findall('^world', multiline=True, ignorecase=True) }}
  {{ 'Hello\nWorld\nhello\nworld' | regex_findall('(?im)^world') }}
  {% endraw %}
  ```

* `String Manipulation`

  ```yml
  {% raw %}
  # cat list into string
  {{ mylist | join(" ")"

  # b64 encode and decode
  {{ lookup('community.general.random_string', length=12) | b64encode }}
  {{ lookup('community.general.random_string', length=12, base64=True ) }}
  {{ some_var | b64decode }}
  {% endraw %}
  ```
