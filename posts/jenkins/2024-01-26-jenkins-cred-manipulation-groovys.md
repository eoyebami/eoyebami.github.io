<h1>Jenkins Credential Manipulation Groovy Scripts</h1>
* Here are recorded groovy scripts created to manipulate Jenkins Credentials without have to go through their complicated api
 - Use cases include the following:
   * Implementation in Jenkins jobs
   * Quick Decryption for debugging
   * Migration of secrets from one Jenkins server to another
   * Helm implementation
<h2>Decrypt all BasicSSHUserPrivateKey Credentials</h2>
* This script will decrypt all `BasicSSHUserPrivateKey Credentials` stored in Jenkins Credential Store

```groovy
def creds = com.cloudbees.plugins.credentials.CredentialsProvider.lookupCredentials(
    com.cloudbees.plugins.credentials.common.StandardUsernameCredentials.class,
    Jenkins.instance,
    null,
    null
)

for(c in creds) {
  if(c instanceof com.cloudbees.jenkins.plugins.sshcredentials.impl.BasicSSHUserPrivateKey){
    println(String.format("id=%s desc=%s key=%s\n", c.id, c.description, c.privateKeySource.getPrivateKeys()))
  }
  if (c instanceof com.cloudbees.plugins.credentials.impl.UsernamePasswordCredentialsImpl){
    println(String.format("id=%s desc=%s user=%s pass=%s\n", c.id, c.description, c.username, c.password))
  }
}
```

<h2>Getting Content of FileCredentials</h2>
* This script will decrypt the contents of a `FileCredentials` stored in the Jenkins Credential Store

```ruby
 import com.cloudbees.plugins.credentials.*;
import com.cloudbees.plugins.credentials.domains.Domain;
import org.jenkinsci.plugins.plaincredentials.impl.FileCredentialsImpl;
println "Jenkins credentials config file location=" + SystemCredentialsProvider.getConfigFile();
println ""
def fileName = "my-secret-file.txt" # Give the fileName specified in the SecretFile
SystemCredentialsProvider.getInstance().getCredentials().stream().
  filter { cred -> cred instanceof FileCredentialsImpl }.
  map { fileCred -> (FileCredentialsImpl) fileCred }.
  filter { fileCred -> fileName.equals( fileCred.getFileName() ) }.
  forEach { fileCred -> 
    String s = new String( fileCred.getSecretBytes().getPlainData() )
    println "XXXXXX BEGIN a secret file with fileName=" + fileName + " XXXXXXXXXXXX"
    println s
    println "XXXXXX END a secret file with fileName=" + fileName + " XXXXXXXXXXXX"
    println ""
  }
```

<h2>Updating Description of FileCredentials</h2>
* This script will update the description of a `FileCredentials` stored in the Jenkins Credential Store

```ruby
import org.jenkinsci.plugins.plaincredentials.impl.FileCredentialsImpl
import com.cloudbees.plugins.credentials.SecretBytes
def updateSecretFileDescription = { cred_id, file_name ->
    // def result = false
    def newDescription = 'CREDS_FILE for rotation'
    def creds = com.cloudbees.plugins.credentials.CredentialsProvider.lookupCredentials(
        com.cloudbees.plugins.credentials.common.StandardCredentials.class,
        Jenkins.instance
    )
    def credentials_store = Jenkins.instance.getExtensionList(
      'com.cloudbees.plugins.credentials.SystemCredentialsProvider'
    )[0].getStore()
    def c = creds.findResult { it.id == cred_id ? it : null }

    if (c) {
        /*def result = credentials_store.updateCredentials(
            com.cloudbees.plugins.credentials.domains.Domain.global(),
            c,
            new FileCredentialsImpl(c.scope, c.id, newDescription, c.fileName, c.secretBytes)
          )
        println "${result}" 
        println "found credential ${c.id}"*/
        try {
          def result = credentials_store.updateCredentials(
            com.cloudbees.plugins.credentials.domains.Domain.global(),
            c,
            new FileCredentialsImpl(c.scope, c.id, newDescription, c.fileName, c.secretBytes)
          )
        println "${result}" 
        println "updated credential ${c.id}'s description"
        } catch (Exception e) {
          result = false
          println "${result}"
          println "failed to update credential ${c.id}'s description"
        }
    }
   // return result
}

updateSecretFileDescription('${credId}', '${fileName}')
```

<h2>Updating Content of FileCredentials</h2>
* This script will update the content of a `FileCredentials` stored in the Jenkins Credential Store

```ruby
import org.jenkinsci.plugins.plaincredentials.impl.FileCredentialsImpl
import com.cloudbees.plugins.credentials.SecretBytes
def password_expired = false
def updateSecretContent = { cred_id, file_name ->
    if (password_expired == true) {
      def String file_content = readFile(file_name)
      def String content = file_content.bytes.encodeBase64().toString()
      def creds = com.cloudbees.plugins.credentials.CredentialsProvider.lookupCredentials(
          com.cloudbees.plugins.credentials.common.StandardCredentials.class,
          Jenkins.instance
      )
      def credentials_store = Jenkins.instance.getExtensionList(
        'com.cloudbees.plugins.credentials.SystemCredentialsProvider'
      )[0].getStore()
      def c = creds.findResult { it.id == cred_id ? it : null }
      if (c) {
          try {
            def result = credentials_store.updateCredentials(
              com.cloudbees.plugins.credentials.domains.Domain.global(),
              c,
              new FileCredentialsImpl(c.scope, c.id, c.description, c.fileName, SecretBytes.fromString(content))
            )
          println "updated credential ${c.id}'s content"
          } catch (Exception e) {
            error "failed to update credential ${c.id}'s content"
          }
      }
  } else {
      println "Password for ${cred_id} has not expired, skipping..."
  }
}
updateSecretContent('${credId}', '${fileName}')
```

<h2>Updating Password for UsernamePassword Credentials</h2>
* This script will update the Password of a `UsernamePasswordCredentials` stored in the Jenkins Credential Store

```ruby
import com.cloudbees.plugins.credentials.impl.UsernamePasswordCredentialsImpl
def changePassword = { id, new_password ->
    def creds = com.cloudbees.plugins.credentials.CredentialsProvider.lookupCredentials(
        com.cloudbees.plugins.credentials.common.StandardUsernameCredentials.class,
        Jenkins.instance
    )

    def c = creds.findResult { it.id == id ? it : null }

    if ( c ) {
        println "found credential ${c.id}"

        def credentials_store = Jenkins.instance.getExtensionList(
            'com.cloudbees.plugins.credentials.SystemCredentialsProvider'
            )[0].getStore()

        def result = credentials_store.updateCredentials(
            com.cloudbees.plugins.credentials.domains.Domain.global(),
            c,
            new UsernamePasswordCredentialsImpl(c.scope, c.id, c.description, c.username, new_password)
            )

        if (result) {
            println "password changed for ${id}"
        } else {
            error("failed to change password for ${id}, failing build ...")

        }
    } else {
      error("could not find credential for ${id}, failing build ...")
    }
}
changePassword('${credID}', '${RANDOM_STRING}')
```

