## Control Tower
- [Overview](#overview)
- [Components](#components)
- [Demo](#demo)

### Overview

![alt text](images/control-tower/image.png)
* AWS `Control Tower` is a layer on top of aws `organizations` that automates the setup of a secure, compliant, multi-account landing zone
    - it provides preconfigured governance controls and centralized visibility for cloud environments
    - `control tower` automates almost the entire process of working with `organizations`
* When you set up a landing zone, `control tower` either creates a new `organization` or uses an existing one
    - you can create Organization OUs and `control tower` registers it and it appears in the `organizations` console too
    - `control tower` authors and attaches them
    - ![alt text](images/control-tower/image-1.png)
* You can configure drift notifications to alert when changes occur that differ from configurations

### Components

* `Landing Zone`: a pre arch environment featuring a root structure, a security OU, and an optional sandbox ox
* `Shared Accounts`: automatically provisions a `log archive` account for centralized logs and an `audit` account for security acces
* `Account Factory`: a built in catalog workflow that standardizes the creation and provisioning of new member accounts
    - allows you to automate the creation of new accounts
* `Controls`: guardrails
    ![alt text](images/control-tower/image-2.png)
    - `preventive`: implemented as `scps` and `iam policies` that block actions outright
    - `detective`: implemented as `aws config rules`
        * flag non compliant resources after the fact
    - `proactive`: cloudformation hooks
        * reject noncompliant resources at deploy time before they are created

### Demo

1. Set up landing zone
    - ![alt text](images/control-tower/image-3.png)
    - ![alt text](images/control-tower/image-4.png)
    * select home region
        - ![alt text](images/control-tower/image-5.png)
    * set region deny settings
        - ![alt text](images/control-tower/image-6.png)
    * select regions you want control tower to govern
        - ![alt text](images/control-tower/image-7.png)

2. Configure OUs
    * define foundational (log-archive and security account) and additional OU
        - ![alt text](images/control-tower/image-8.png)

3. Configure shared accounts
    * management account, log-archive, and audit
        - ![alt text](images/control-tower/image-9.png)
        - ![alt text](images/control-tower/image-10.png)

4. AWS Account access configuration
    - ![alt text](images/control-tower/image-11.png)
    * define cloudtrail config
        - ![alt text](images/control-tower/image-12.png)
    * define log config for s3
        - ![alt text](images/control-tower/image-13.png)
    * define kms encryption
        - ![alt text](images/control-tower/image-14.png)

5. Create Landing Zone
    - ![alt text](images/control-tower/image-15.png)
    - ![alt text](images/control-tower/image-16.png)

6. You'll see control tower created those accounts and a control tower admin account for those accounts 
    * you'll receive emails to accept invitation to activate account 
        - ![alt text](images/control-tower/image-17.png)

7. In control tower you'll see a user portal url you can use for accessing the landing zone
    - ![alt text](images/control-tower/image-18.png)
    - ![alt text](images/control-tower/image-19.png)

8. You can create new aws accounts under `account factory` in the control tower
    - ![alt text](images/control-tower/image-20.png)
    - ![alt text](images/control-tower/image-22.png)
    - ![alt text](images/control-tower/image-23.png)
    - ![alt text](images/control-tower/image-24.png)
    * you can define default network configurations in `account factory`
        - ![alt text](images/control-tower/image-21.png)
