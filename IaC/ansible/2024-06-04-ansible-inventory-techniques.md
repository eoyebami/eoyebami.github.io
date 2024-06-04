<h1>Ansible Inventory Techniques</h1>
* There are multiple techniques you can use when defining an `Ansible` inventory file

<h2>Inventory Techniques</h2>
* Creating groups within groups

  ```ini
  # calling alias that are predefined elsewhere
  [web_servers]
  web1
  web2

  [db_servers]
  db1

  [all_servers:children]
  web_servers
  db_servers
  ```

  ```yml
  web_servers:
    hosts:
      web1:
      web2:
  db_servers:
    hosts:
      db1:
  all_servers:
    children: # children object allows you to define other groups
      web_servers:
      db_servers:
  ```

* Define a host in multiple groups

  ```ini
  # calling alias that are predefined elsewhere
  [web_servers]
  web1
  web2
  ldap1 # lam server configure here as well

  [ldap_servers]
  ldap1

  [db_servers]
  db1
  ldap1
  ```

  ```yml
  web_servers:
    hosts:
      web1:
      web2:
      ldap1:
  ldap_servers:
    hosts:
      ldap1:
  db_servers:
    hosts:
      db1:
      ldap1:
  ```

* Adding ranges of hosts

  ```ini
  [web-servers]
  web[1:50].example.com # creates a range of hosts from 1 to 50

  [db-servers]
  db[1:50:2].example.com # creates a range of hosts from 1 to 50, going by increments of 2, so 1,3,5...n
  ```

  ```yml
  web-servers:
    hosts:
      web[1:50].example.com:
  db-servers:
    hosts:
      db[1:50:2].example.com:
  ```

