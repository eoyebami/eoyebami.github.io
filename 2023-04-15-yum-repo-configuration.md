<h2>Yum Repo Configuration</h2>

Appstream: used to installed the application
BaseOS: used to install base packages for OS

Go into /etc/ (which contains all the configuration files), and go into the yum.repo.d folder to register the newly create repositories)
1. Create a repo.d (configuration for yum repo) file (vi Appstream.repo)
2. Follow the below template
 
 ex:
```
    [AppStream]
    name="App Stream Repo"           (name of your repo)
    baseurl=file:///path/to/yum/repo (where your yum repo is: yum will look for the package files (RPMs) in this location)
    enabled=1                        (repo is enabled)
    gpgcheck=0                       (gpgcheck is disabled: specifies whether or not yum should verify the digital signature of the packages before installing)
    gpgkey=file:///path/to/gpgkey    (specifies path where the repos GPG public key can be found to verify digital signature)
```

3. Do a yum clean all (which will remove all cached packages and headers of all enabled yum repositories). To temporarily enable a repo to clear the cache, added the --enablerepo="*" flag. TO check the cache on the yum repos go to /var/cache/
4. Yum replist (will give you a list of all the yum repos) 
5. Yum update (will update your server with the packages from the yum repo

Extras: 
  Why are gpgkeys important? A gpg key can be used to create a digitial signature that can verify the authenticity of files or messages (by signing a the packages in your repo, you provide a tamper-evident seal that lets others know that the package has not been alterned since you signed it, only others with the private key can read the message)
  What key is specified in the yum.repo.d conf? The key specified is the public key, the creator of the repo signs all the packages in the repo with the private key and then specifies the public key in the GPG keyring. In the repo.d conf for the yum repo, the gpg public key can be specified, else yum will use the default key in the GPG keyring. 


<h2>Project:</h2> 
   1. Create your own yum repo
   2. Create a gpgkey and give your yum repo a digital signature
   3. See what happens when you try to install packages from the repo, when gpgcheck is enabled but the repo has no digital signature
   4. See what happens when you try to install packages from the repo, when gpgcheck is enabled and the repo has a digital signature

How-to Installing gpu
  1. Install gnupg2        ```(sudo yum install gnupg2)```
  2. Start the gpg-agent   ```(gpg-agent --daemon)```
  3. Test connection       ```(gpg-connect-agent)```
  4. Create gpg key        ```(gpg --full-gen-key)```
  5. Export public key     ```(gpg --export -a "Excellent Oyebami" > "name_of_key.asc"   Private key will be uploaded to home directory in /home/$USER/.gpupg/*.gpg)```
  6. Create .rpmmacros file (follow below template)
    ex:
```
      %_signature gpg             (type of key that will be used when signing)
      %_gpg_name "Your Name"      (Name you used when creating your key)
      %_gpg_path /path/to/gpg/key (path to your public key)
      %__gpg /usr/bin/gpg         (binary for gpg command)
```

  7. Import key to rpm db   ```(rpm --import /path/to/key.asc)```
  8. Build rpm              ```(if necessary)```
  9. Sign key               ```(rpmsign --addsign /path/to/rpmpackage)```
    Note: may fail with "define %_gpg_name" if so define in command ```rpm --define "_gpg_name <your email or name>" --addsign <RPM to sign>```
    Note: when you sign the rpm, it is signed with your private key. When you import the key to rpm db, it imports the public key into the gpg keyring for the repository
  10. Create the yum.repo.d (for the repository you created)
    ex:
```
      [ExcellentOS]
      name="Excellent HTTPD"
      baseurl=file:///opt/ExcellentOS
      enabled=1
      gpgcheck=1
      gpgkey=file:///opt/ExcellentOS/ezkey.asc
```  
  11. Install pkg          ```(yum install httpd --enablerepo=ExcellentOS)```
