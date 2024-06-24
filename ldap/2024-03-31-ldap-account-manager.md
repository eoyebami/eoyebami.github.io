<h1>LDAP Account Manager(LAM)</h1>
 
* LAM is a webfrontend for managing entries in an LDAP server
* Its designed to make managing an ldap server as easy as possible
<h2>Installation</h2>
 
* Assuming you are using an Ubuntu/Debian Machine

```console
sudo apt update && apt upgrade -y
sudo add-apt-repository ppa:ondrej/php
sudo add-apt-repository ppa:ondrej/apache2
sudo add-apt-repository ppa:ondrej/nginx
sudo apt update 
sudo apt-get install apache2 php -y # make sure php is 8.0+
sudo apt-get install ldap-account-manager -y

# enable and start service
sudo systemctl start apache2 
sudo systemctl enable apache2

# check apache2 logs at /var/logs/apache2 for trooubleshooting
```

* Login into the UI, and set the default profile in the configurations
  - modify general settings
  - modify account types
* Set defaults for Organization and Admin User as defined [here](https://eoyebami.github.io/2024-03-31-configure-ldap.html)
  - Everything else can be defined in the UI
