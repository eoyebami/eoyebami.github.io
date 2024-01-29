<h1>Passing IAM Roles EKS Pods</h1>
* For clusters running on AWS that will need access to AWS resources, you can annotate `ServiceAccounts` with IAM roles to allow pods to utilize the roles through the account
* Because of the v1.22 release of kubernetes that moonlit the `Token Request API`, which allows a fully compliant OIDC JWT token to be issued and mounted unto pods as `Projected Volumes`
* AWS EKS comes installed with a identity mutating webhook that injects the tokens into the pod using an `IAM OIDC Identity Provider`, by listening for API calls that are specific for creating pods.
  - This provider will authenticate and issue the tokens for the EKS cluster
* By default, `ServiceAccounts` in kubernetes use the `api-server` as the default audience to validate the token
  - The primary token is used to retrieve a secondary token from the OIDC provider
  - AWS's mutating webhook will mount that secondary token that will use `sts.amazonaws.com` as its audience, so that after validation, the AWS STS `AssummedRoleWithWebIdentity` API will send temporary creds to access the role
  - One token will be validated by kubernetes to make calls to `kube-apiserver` and the second will be validated by AWS to make `aws sts` calls to it
  - The mutating webhook will iso inject environment variables necessary to make the assume-role call 

* Create an OIDC provider for your EKS Cluster, this should automatically be created at cluster startup
  - This will act as the issuer for the JWT tokens in the cluster
* Create the `IAM` Role in AWS
  - Add the necessary permissions and modify the `Trust Relationships` to include the OIDC JWT Token 

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Federated": "arn:aws:iam::111122223333:oidc-provider/oidc.eks.us-east-2.amazonaws.com/id/xxxx"
      },
      "Action": "sts:AssumeRoleWithWebIdentity",
      "Condition": {
        "StringEquals": {
          "oidc.eks.us-east-2.amazonaws.com/id/xxxx:aud": "sts.amazonaws.com",
          "oidc.eks.us-east-2.amazonaws.com/id/xxxx:sub": "system:serviceaccount:default:<name-of-service-account"
        }
      }
    }
  ]
}
```

* Create the `ServiceAccount` and annotate it with the aws role you'd like to attach

```
apiVersion: v1
kind: ServiceAccount
metadata: 
  annotations:
    eks.amazonaws.com/role-arn: arn:aws:iam::<account-id>:role/<role-name>
    eks.amazonaws.com/token-expiration: 3600 # optional, default expiration for token before rotation
    eks.amazonaws.com/sts-regional-endpoints: "true" # optional 
  name: <name-of-servicce-account>
```

* Specify the `ServiceAccount` Pod Manifest File

```
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: eks-iam-test3
spec:
  serviceAccountName: <service-account-name>
  containers:
    - name: my-aws-cli
      image: amazon/aws-cli:latest
      command: ['sleep', '36000']
  restartPolicy: Never
EOF
```
