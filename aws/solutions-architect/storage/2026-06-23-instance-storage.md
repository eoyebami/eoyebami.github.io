## Instance Storage
- [Overview](#overview)

### Overview

* An `instance store` provides temporary block level storage for your `ec2 instances`, depending on the instance type. These `instance stores` are located physically on the disks attached to the host computer, its not abstract like `ebs`
* Because of the nature of how the `instance store` is physcially on the host machine, even if the `ec2` itself were to go down and come back up, it will still have access to the store
    - That is, as long as the instance remains on the host machine. If the instance goes down and comes back up on another host, it will not be able to access that `instance store`. Hence, why `instance stores` are meant for temporary use.
        * If the `ec2` gets moved, it will have access to the `instance store` on that new host, but none of the data from the previous store on the other host

* NOTES:
    - Info in data persistence can be found [here](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-store-lifetime.html)
        * reboots won't cause the `ec2` to be moved to another host
        * shut down and a startup will move the instance to another host