<h1>ECR Image Security</h1>
* AWS ECR Image names follow a specific format: `<account-id>.dkr.ecr.<region>.amazonaws.com/<repository>`
  - `111111111111.dkr.ecr.us-east-2.amazonaws.com/nginx`: would be the full image name for nginx
    * `111111111111.dkr.ecr.us-east-2.amazonaws.com/`: AWS ECR Default registry format
    * `nginx`: repository
<h2>Private Repository</h2>
* To pull images from a private repository in ecr, you authenticate through your permissions, either added to the Registry itself or those assigned to your user
  - Minimum permissions a user must have to pull ecr images from a repository:
  ```json
  {
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ecr:BatchCheckLayerAvailability",
                "ecr:BatchGetImage",
                "ecr:GetDownloadUrlForLayer",
                "ecr:GetAuthorizationToken"
            ],
            "Resource": "*"
        }
     ]
  }
```

* To allow another AWS Account to pull images from another, you modify the repositories permissions to include that account
```json
{
  "Version": "2012-10-17",
  "Statement": [
      {
          "Effect": "Allow",
          "Principal": {
            "AWS": "arn:aws:iam::<account-id>:root"
          "Action": [
              "ecr:BatchCheckLayerAvailability",
              "ecr:BatchGetImage",
              "ecr:GetDownloadUrlForLayer",
              "ecr:GetAuthorizationToken"
          ],
          "Resource": "*"
      } 
   ]
}
```

2. Specify the Image in the Manifest File
  ```yml
  spec:
    containers:
    - name: nginx
      image: <account-id>.dkr.ecr.<region>.amazonaws.com/<repository>
  ```

<h2>Public Repository</h2>
* Anyone can pull images from public repositories without authentication
  - However just like `S3` buckets, policies can be applied to the repository to restrict who can do what within that repo
