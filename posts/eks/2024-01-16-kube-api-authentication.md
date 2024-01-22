<h1>Authentication</h1>
* In other to interact with the cluster in anyway, you first need to authenticate through the `kube-apiserver`
* Now there are various ways to do this
  - static password file
  - static token file
  - certificates
  - identity services
* Note: After authentication, you'll still need to made the user/group using RBAC, so they can access resources
  - Its a 2 step process
   * Authenication: verify identity to the kube-apiserver
   * Authorization: permissions necessary to make calls to cluster resources
<h2>Authentication: Static Password File</h2>
* You'll need to created a user-details.csv file containing all relevant information concerning a user and map that to the `kube-apiserver` configuration
  - `--basic-auth-file=user-details.csv`
- Ex:

```
password123,user1,u0001,group1 # group column is optional
password123,user1,u0002,group2
```

* To then authenticate with a user you specific in the file run a curl on the cluster's endpoint
  - `curl -v -k https//host-ip:6443/api/v1/pods -u "user1:password123"`
<h2>Authentication: Static Token File</h2>
* Similar to the static password file, you can generate a static token file, that will include a token as a column
  - `--token-auth-file=user-token-details.csv
- Ex:

```
xxxx,user1,u0001,group1
xxxx,user2,u0002,group2
```

* To then authenticate with a token you specified in the file run a curl on the cluster's endpoint
  - `curl -v -k https://host-ip:6443/api/v1/pods --header "Authorization: Bearer: <token>"`
