
<h1>Configure SSL/TLS for LDAP</h1>
* Enabling SSL for LDAP server will will allow you to use the LDAPs protocol, similarly to how HTTPS works with HTTP
  - By default LDAP listens on 389
  - LDAPs listens on port 636
* In my case, since this is a purely internal LDAP server, I will be using a self-signed key to enable SSL on my ldap server

<h2>Creating Cert for LDAP</h2>
* Generate the root CA key
  ```console
  # mkdir for certs and generated using rsa algorithm
  mkdir /etc/ldap/certs/ca
  cd /etc/ldap/certs/ca/
  openssl genrsa -out ca.key 2048
  ```

* Generate self-singed root CA certificate
  ```console
  # gave it a validity period of 365 days
  # fill out prompts
  # CN should be FQDN, run hostname --fqdn
  openssl req -x509 -sha256 -new -nodes -key ca.key -days 365 -out ca.crt

  # verify cert, outputs cert into as text
  openssl x509 -in ca.crt -text
  ```

* Genereate ldap server private key
  ```console
  openssl genrsa -out ldap-server.key 2048  
  ```

* Generate csr from ldap server private key
  ```console
  # fill out prompt
  openssl req -new -key ldap-server.key -out ldap-server.csr
  ```

* Sign CSR for ldap-server with root CA cert
  ```console
  # sign csr with ca.crt and ca.key, set validity to 365 days 
  openssl x509 -req -in ldap-server.csr -CA ca/ca.crt -CAkey ca/ca.key -CAcreateserial -out ldap-server.crt -days 365 -sha256
 
  # verify cert, outputs cert into as text
  openssl x509 -in ldap-server.crt -text
  ```

* Grant `openldap` read access to those certs
  ```console
  chown -R openldap: /etc/ldap/certs/
  ```

* Add certs to ldap using ldif file
  ```console
  # add this config to a file called ssl-ldap.ldif
  dn: cn=config
  changetype: modify
  add: olcTLSCACertificateFile
  olcTLSCACertificateFile: /etc/ldap/certs/ca/ca.crt
  -
  replace: olcTLSCertificateFile
  olcTLSCertificateFile: /etc/ldap/certs/ldap-server.crt
  -
  replace: olcTLSCertificateKeyFile
  olcTLSCertificateKeyFile: /etc/ldap/certs/ldap-server.key

  # run ldapmodify
  ldapmodify -Y EXTERNAL -H ldapi:/// -f ssl-ldap.ldif
  ```

* Configure SSL in `/etc/default/slapd` file
  ```console
  SLAPD_SERVICES="ldap:/// ldapi:/// ldaps:///"
  ```

* Add ldap cert update to `/etc/ldap/ldap.conf` file
  ```console
  # TLS_CACERT    /etc/ssl/certs/ca-certificates.crt
  TLS_CACERT    /etc/ldap/certs/ldap-server.crt
  TLS_REQCERT   allow
  ```

* Restart slapd service: `sudo systemctl restart slapd`
  ```console
  # confirm functionality
  ldapsearch -x -H ldaps://<server-ip> -b "dc=ldapserver"

  # confirm port is being listened on
  netstat -antup | grep -i 636
  ```
