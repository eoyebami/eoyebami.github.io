<h2>Assuming a role!</h2>

In order to enable a user to assume a role: 

1. It will first need the `sts:AssumeRole` permission enabled. Here is an example:

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowAssumeRole",
            "Effect": "Allow",
            "Action": "sts:AssumeRole",
            "Resource": "<ARN of the role to be assumed>"
        }
    ]
}
```

This will allow the role to assume the specified role in the resource block. 

2. After this, a trust relationship needs to be formed between the role `(that is to be assumed)` and the resource `(which is attempting to assume the role)`. Below is an example of the added permissions to the trust relationship of the role `(which is to be assumed)`.

```
{
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:iam::738921266859:role/ssm-ec2" (this could either be a user or a role)
            },
            "Action": "sts:AssumeRole"
        }
```
This will allow this role to be assumed by that specific resource. 

<h2>Creating a Cross-Account Role</h2>

In order to create a cross account role:

1. You first need to create policy `(which will later be attached to the cross account role)` in `Account A`. This policy should have all necessay permissions that the service in `Acccount B` is trying to access.

2. Next you create the cross account role. Below is a table describing how the role should be created:


  | Sections                 | Input                                     |
  | ---                      | ---                                       |
  |`Trusted Entity type`     | AWS account #                             |
  |`Permissions Policies`    | Select policy with necessay permissions   |
  |`Name, Review, and Create`| Name Role, Review Role, and Create        |

3. Make sure the service in `Account B` has the `sts:AssumeRole` permission
4. Assume role. Command below will retrieve temporary credentials and export them into env:
```
export $(printf "AWS_ACCESS_KEY_ID=%s AWS_SECRET_ACCESS_KEY=%s AWS_SESSION_TOKEN=%s" \
$(aws sts assume-role \
--role-arn arn:aws:iam::123456789012:role/MyAssumedRole \
--role-session-name MySessionName \
--query "Credentials.[AccessKeyId,SecretAccessKey,SessionToken]" \
--output text))
```
Store it as a function to call at any moment.
