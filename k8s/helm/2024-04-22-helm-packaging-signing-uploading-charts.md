<h1>Packaging, Signing, and Uploading Helm Charts</h1>
 
* Once you chart is created and ready to be uploaded to a public repository, you'll need to first package and sign the chart

<h2>Packing and Signing Helm Chart</h2>
 
* Next we'll want to cryptographically sign our helm charts, so when people pull the chart they can verify that the chart is ours through our public key
* First, generate the key you'd like to sign with
  - `gpg --quick-generate-key "EZ"`: generates a key for signing 
    * Upon create this public key can be uploaded to an openpgp server for anyone to download and verify your package
  - `gpg --full-generate-key "EZ"`: generates key for signing
    * Recommended for production environments
    * You can set expiration dates, metadata, etc. 
  - NOTE: most modern linux machines use `GnuPG v2` whi ch stores secret keyrings using a new format `kbx` on the default location `~/.gnupg/pubring.kbx`
    * `gpg --export >~/.gnupg/pubring.gpg`: exports pubring to legacy gpg format
    * `gpg --export-secret-keys >~/.gnupg/secring.gpg`: exports secring to legacy gpg format
* Once we have this key ready, we can package the chart and set it to sign with the key we created
  - `helm package --sign --key 'EZ' --keyring ~/.gnupg/secring.gpg ./nginx-test`: packages and signs helm chart
    * With a `--sign` flag set and additional `.tgz.prov` file will be created that contains the sha of the chart which should also match the sha of the packaged chart 
    * Run a `sha256sum` on your packaged helm chart file, and the shas should match
* Now we can verify the chart by running `helm verify`
  - `helm verify ./nginx-chart-0.1.0.tgz`: verifies the helm chart againsts its pubring (which will need to be downloaded first)
    * Will default to location `~/.gnupg/pubring.gpg`
  - `helm verify --keyring ~/path/to/pubring ./nginx-chart-0.1.0.tgz`: verifies helm chart against pubring specified with `--keyring` flag
  - NOTE: in a real env a user would first retrieve the key by running `gpg --recv-keys --keyserver <server-hostname> <UID>`

<h2>Uploading Chart to Hub</h2>
 
* Once you've packaged and signed you're chart, you are now reading to upload it to the Artifact Hub
* First, you'll need to generate an `index.yaml` file that will also be published to the Artifact Hub
  - Create a new directory and copy the helm chart `.tgz` and `.tgz.prov` into that directory
  - `helm repo index chart-dir/ --url https://url-of-helm-repo`: creates `index.yaml`
* Then push to a remote registry
  - `helm push <chart-name> <remote>`
