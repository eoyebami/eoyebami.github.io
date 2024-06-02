<h1>Configuring LDAP</h1>
* There are a variety of steps that need to first be taken when configuring an ldap server

<h2>Installing LDAP</h2>
* I'm under the pretense that a debian/ubuntu linux machine will be used

```console
sudo apt install slapd ldap-utils # install ldap
slappasswd -h {SSHA} # set passwd
sudo dpkg-reconfigure slapd # set domain to server fqdn, password, etc

# Create LDIF file and load the schema files required for accounts
include file:///etc/ldap/schema/cosine.ldif

include file:///etc/ldap/schema/nis.ldif

include file:///etc/ldap/schema/inetorgperson.ldif

# Load the HDB (hierarchical database) backend modules
dn: cn=module,cn=config
objectClass: olcModuleList
cn: module
olcModulepath: /usr/lib/ldap
olcModuleload: back_hdb

# Configure the database settings
dn: olcDatabase=hdb,cn=config
objectClass: olcDatabaseConfig
objectClass: olcHdbConfig
olcDatabase: {1}hdb
olcSuffix: dc=mydom,dc=com
# The database directory must already exist
# and it should only be owned by ldap:ldap.
# Setting its mode to 0700 is recommended
olcDbDirectory: /var/lib/ldap
olcRootDN: cn=admin,dc=mydom,dc=com
olcRootPW: {SSHA}lkMShz73MZBic19Q4pfOaXNxpLN3wLRy
olcDbConfig: set_cachesize 0 10485760 0
olcDbConfig: set_lk_max_objects 2000
olcDbConfig: set_lk_max_locks 2000
olcDbConfig: set_lk_max_lockers 2000
olcDbIndex: objectClass eq
olcLastMod: TRUE
olcDbCheckpoint: 1024 
olcAccess: to attrs=userPassword
  by dn="cn=admin,dc=mydom,dc=com"
  write by anonymous auth
  by self write
  by * none
olcAccess: to attrs=shadowLastChange
  by self write
  by * read
olcAccess: to dn.base=""
  by * read
olcAccess: to *
  by dn="cn=admin,dc=mydom,dc=com" # will lock down read access to this admin user
  write by * read
```

* Run `ldapadd` to add entries
  - `ldapadd -Y EXTERNAL -H ldapi:/// -f init.ldif`

<h2>Setting Base OU</h2>
* You'll need to set the `Peoples` and `Groups` ou before you can start creating groups and users

```console
dn: ou=People,dc=example,dc=com
objectClass: organizationalUnit
ou: People

dn: ou=Group,dc=example,dc=com
objectClass: organizationalUnit
ou: Group
```

* Run `ldapadd` to add entries
  - `ldapadd -x -D cn=admin,dc=mydom,dc=com -W -f ou-init.ldif`

<h2>Add a Group to LDAP</h2>
* Lets add a group to our OU

```console
# Group employees
dn: cn=employees,ou=Group,dc=mydom,dc=com
cn: employees
gidNumber: 626
objectClass: top
objectclass: posixGroup
```

* Run `ldapadd` to add entries
  - `ldapadd -x -D cn=admin,dc=mydom,dc=com -W -f group.ldif`
* Confirm creation
  - `ldapsearch -x -LLL -H ldap:/// -b dc=mydom,dc=com`

<h2>Add User to LDAP</h2>
* Lets create a user in our `People` OU

```console
# User ez
dn: uid=ez01,ou=People,dc=mydom,dc=com
changetype: add
objectClass: top
objectClass: person
objectClass: organizationalPerson
objectClass: inetOrgPerson
uid: ez01
givenName: EZ
sn: O
cn: ez
```

* Run `ldapadd` to add entries
  - `ldapadd -x -D cn=admin,dc=mydom,dc=com -W -f user.ldif`
* Confirm creation
  - `ldapsearch -x -LLL -H ldap:/// -b dc=mydom,dc=com`

<h2>Add User to Group in LDAP</h2>
* Lets add our user to our group

```console
dn: cn=devops,ou=Group,dc=mydom,dc=com
changetype: modify
add: memberUid
memberUid: ez01
```

* Run `ldapadd` to add entries
  - `ldapmodify -x -D cn=admin,dc=mydom,dc=com -W -f user-to-group.ldif`
* Confirm creation
  - `ldapsearch -x -LLL -H ldap:/// -b dc=mydom,dc=com gidNumber=626`

