## Elastic Beanstalk
- [Overview](#overview)
- [Environments](#environments)
- [Supported Platforms](#supported-platforms)
- [Features](#features)
- [Hands On](#hands-on)

### Overview

![alt text](images/beanstalk/image.png)

* AWS `Elsatic Beanstalk` was created so you can quickly deploy and manage applications in the cloud without having to understand all the infra that would be necessary to launch that application otherwise
* `Elastic Beanstalk` is going to deploy all the infra for you, so all you need to focus on is the application
    - You pass it your application code and define the configuration and `beanstalk` will handle creating the `ec2`, `rds`, etc.

### Environments

* In `beanstalk` there is a concept known as `environments` which is just the aggregate of the different underlying resources needed to deploy your app
    - great for seperating dev, testing, and prod deploys 

* You can then manage these environments
    - monitor app health
    - update version of code running
![alt text](images/beanstalk/image-2.png)

### Supported Platforms

* `Beanstalk` supports all the major platforms
![alt text](images/beanstalk/image-1.png)

### Features

![alt text](images/beanstalk/image-3.png)

### Hands On

1. Go to `Beanstalk` console
    - ![alt text](images/beanstalk/image-4.png)

2. Create an application
    - ![alt text](images/beanstalk/image-5.png)

3. Create an environment for that application
    - ![alt text](images/beanstalk/image-6.png)
    - ![alt text](images/beanstalk/image-7.png)
    - You can provide a domain name
        * ![alt text](images/beanstalk/image-8.png)
    - Specify platform and app code (uploading code: accepts as zip file or war file for java, there is a size limit)
        * ![alt text](images/beanstalk/image-9.png)
    - Specify presets
        * ![alt text](images/beanstalk/image-10.png)

4. Configure Service Access
    - `Beanstalk` will need a role that it will use to manage those resources it will create
        * ![alt text](images/beanstalk/image-11.png)

5. Configure Networking and DB
    - ![alt text](images/beanstalk/image-12.png)
    - Configure DB
        * ![alt text](images/beanstalk/image-13.png)
        * ![alt text](images/beanstalk/image-14.png)

6. Configure Instance Traffic and Scaling
    - ![alt text](images/beanstalk/image-15.png)
    - ![alt text](images/beanstalk/image-16.png)
    - Configure auto scaling
        * ![alt text](images/beanstalk/image-17.png)
        * Arch and instance type can also be selected
            - ![alt text](images/beanstalk/image-18.png)
    - Configure LB settings
        * ![alt text](images/beanstalk/image-19.png)
        * ![alt text](images/beanstalk/image-20.png)
        * ![alt text](images/beanstalk/image-21.png)
        * ![alt text](images/beanstalk/image-22.png)
        * ![alt text](images/beanstalk/image-23.png)

7. Configure Monitoring, Updates, and Logging
    - ![alt text](images/beanstalk/image-24.png)
    - ![alt text](images/beanstalk/image-25.png)
    - ![alt text](images/beanstalk/image-26.png)

8. Deploy Beanstalk
    - ![alt text](images/beanstalk/image-27.png)
