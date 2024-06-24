<h1>Introduction to LDAP</h1>
 
* Before diving into `LDAP` there are a couple key words we must learn
  - `Active Directory`: used to provide authentication, user and group management
    * A directory service db
  - `Lightweight Directory Access Protocol`: A secure service daemon that runs over TCP protocal 
    * A protocol used to communicate with `AD`
* Any system configured to use an LDAP server will follow a client-server model
  - In which a client will enter authentication information and the LDAP server will compare user creds to its LDAP databases to verify
<h2>LDAP</h2>
 
* LDAP is a protocol used to access information directories and lets users access centrally stored information over a network
* LDAP directory server stores information in a directory-based db that is optimized for searching and browsing
  - The db entries are arranged in a hierarchical tree-like structure, where each directory stores information such as names, address, telephone numbers, network information, etc. 
* This smallest unit of infromation in an LDAP directory is an entry, and entries can have more than one attribute
  - Each attribute of an entry has a name
    * `o`: organization
    * `dc`: domain component; defined when a domain is set for the server (which in my case is the hostname
    * `cn`: common name
    * `ou`: organizational unit
    * `objectClass`
    * `dn`: distinguished name; used to identify an entry in LDAP
* LDAP data is stored in binary, `LDAP Data Interchange Format (LDIF)` is a plain-text representation of a LDAP entry, that you can used to import/export/modify LDAP data
<h2>LDAP Structure</h2>
 
* `o`: is the organization, otherwise known as the root of a LDAP directory
* `ou`: sub domain within a LDAP organization
  - `Distribution Groups`
  - `Groups`: a sub category within an ou, needs to be defined before groups can be defined in an `o`
  - `People`: is sub catergory within an ou, needs to be defined before users can be added to an `o`
* `dc`: is the domain component, `.` in the dc will act as file separators
  - Ex: if your `dc` is set to `example.com`; then LDAP will recognize it as `dc=example,dc=com`
* `dn`: identifier for an entry into the LDAP server
* `objectClass`: used to indicate what ype of object an entry is, which in turn will allow you to define attributes for that specific object class
  - `Abstract Object Classes`: specify a set of required and optional attribute types, but are supplemental object classes
  - `Structural Object Classes`: specifiy the main type of object that an object represents `(ie. Groups, People, Distribution Groups)`
  - `Auxiliart Object Classes`: used to provide information about additional characteristics for an entry
  - Ex:
    * `objectClass: organizationalUnit`: defines this entry as an `ou`
    * `objectClass: organization`: defines this entry as the `o`
    * `objectClass: posixGroup`: defines a group within the `Groups ou`
    * `objectClass: organizationalPerson`: used to define a subcategory in the `Peoples ou`
    * `objectClass: inetOrgPersion`: used to define a user in the `Peoples ou`
  - Check [here](https://www.zytrax.com/books/ldap/ape/#posixaccount) for more info on `objectClass`
