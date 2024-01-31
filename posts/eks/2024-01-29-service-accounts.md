<h1>Service Accounts</h1>
* Service accounts are used by services to make calls to the `kube-apiserver`
  - Ex:
    * Jenkins using a service account to make deployments to the cluster
    * Prometheus using a service account to pull metrics

1. First you would need to create a service account
  - `kubectl create serviceaccount jenkins`: will create a service account called jenkins
    * Because of v1.24, tokens are no longer automatically generated for each serviceaccount
    * You'll need to manually generate the jwt token by running `kubectl create token jenkins` which will create a jwt token for that service account that will have an expiry date of 1hr
    - `kubectl create token jenkins --duration 10h # can be h or s`
    - Confirm by putting jwt token [here](https://jwt.io/)
    * If you want to generate a long-live api token, you can manually create a secret for it
     
    ```yml
      cat <<EOF | kubectl apply -f -
      apiVersion: v1
      kind: Secret
      metadata:
        name: jenkins-secret
        annotations:
          kubernetes.io/service-account.name: jenkins
        type: kubernetes.io/service-account-token
      EOF
    ```

  * This token is used by any service using this `ServiceAccount` to authenticate to the `kube-apiserver`
    - `curl -v -k https://host-ip:6443/api/v1/pods --header "Authorization: Bearer: <token>"`

2. Next you would create a `Role` and bind to the `ServiceAccount` with `RoleBinding`

   ```yml
   cat <<EOF | kubectl apply -f -
   apiVersion: rbac.authorization.k8s.io/vi
   kind: Role
   metadata:
     name: jenkins
     namespace: default # should be defined because roles are namespace bound
   rules: 
   - apiGroups: [""] # leaving it blank will denote it as core group
     resources: ["pods"]
     resourceNames: ["my-pod"]
     verbs: ["list", "get", "create", "update", "delete"] # were giving the role these action to the pod resource named "my-pod"
   - apiGroups: ["apps"] # using the apps named group
     resources: ["deployments", "replicasets"]
     verbs: ["get", "list", "create", "patch", "update"]
   EOF

   cat <<EOF | kubectl apply -f -
   apiVersion: rbac.authorization.k8s.io/v1
   kind: RoleBinding
   metadata:
     name: jenkins
   subjects:
   - kind: ServiceAccount
     name: jenkins # name of serviceaccount
     apiGroup: rbac.authorization.k8s.io
   roleRef:
     kind: Role
     name: jenkins
     apiGroup: rbac.authorization.k8s.io
   EOF
   ```

3. Specify the `ServiceAccount` in the Manifest File
  * In the case that your service that requires this `ServiceAccount` is running on that cluster, you will need to specify the account in the manifest file

   ```yml
   spec: 
     serviceAccount: jenkins
     serviceAccountName: jenkins
     containers:
     - name: jenkins
       image: jenkins
       volumeMounts: # will be automounted by Token Request API
       - mountPath: /var/run/secrets/tokens
         name: jenkins-token
     volumes:
     - name: jenkins-token # the token for the serviceAccount will be automounted and rotated at expiration by Token Request API
       projected:
         sources:
         - serviceAccountToken:
             path: jenkins-token
             expirationSeconds: 7200
   ```

  * By default, each namespace in your cluster will have a default `ServiceAccount` that is restricted in its authorization
    - This `ServiceAccount's` token will be projected mounted unto every pod created into your cluster at the path `/var/run/secrets/kubernetes.io/serviceaccount/token
    - This will be overwritted by whatever `ServiceAccount` you specified in the pod's manifest file
    - You can also choose not to mount any accounts at all by replace the `serviceAccountName` field with a `automountServiceAccountToken: false` field
