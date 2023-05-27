<h1>Swap Space</h1>
A swap area is an area of the disk that is made avaliable to linux if the system runs our of memory (RAM). With space space, Linux can temporarily swap out data from RAM to the swapp area and then get it back when needed. Though you will take a performance hit when doing this
<h3>Swappiness</h3>
You can set modify when swap is used based on the ammount of memory allocation using swappiness. Which determines how aggressively the system swaps out memory pages to the swap area
* Swappiness values ranges from 1-100
  - THe higher teh swappiness, the more likely the system is to utilize swap 
* Check current swappiness value
  - `sudo cat /proc/sys/vm/swappiness`
* To temporarily change the swappiness value, you must use the `sysctl` command 
  - `sudo sysctl vm.swappiness=30`
  - this change will reset at next system reboot
* To permanently change the swappiness value, you must hardcode it in `/etc/sysctl.conf` file
  - Add line `vm.swappiness=30`
  - This value will be applied ay next system reboot
<h3>How to create a swap area</h3>
* Use `free -m` to see how much avaliable memory you have, including how much avaliable swap space you have
```
[excellent@ip-172-31-56-105 eoyebami.github.io]$ free -h
               total        used        free      shared  buff/cache   available
Mem:           963Mi       193Mi        59Mi       2.0Mi       711Mi       615Mi
Swap:             0B          0B          0B
```

* Use `dd` command to create the swap file
- `dd if=/dev/zero of=/backups/myswap bs=100M count=10`
  * `dd` is a cli tool for copying and converting data; can be used for disk-to-disk cloning
  * `if` specifies the path where `dd` is reading from
  * `/dev/zero` is a special device file that provides an endless stream of null bytes
  * `of` specifies the path where `dd` is writing to, here we specify where we want the swapfile to be created
  * `bs` is the block size, what were we allocating per block to the swap file
  * `count` is how many blocks
- conversion total-disk-space-allocation=(bs * count)
```
[excellent@ip-172-31-56-105 backups]$ sudo dd if=/dev/zero of=/backups/myswap bs=100M count=10
10+0 records in
10+0 records out
1048576000 bytes (1.0 GB, 1000 MiB) copied, 14.004 s, 74.9 MB/s
```

* Modify the file permissions to ensure no security and access control
  - `chmod 600 /backups/myswap`
* Format the swap file
  - `sudo mkswap /backups/myswap`
* Enable the swap file 
  - `sudo swapon /backups/myswap`
```
sudo mkswap myswap 
mkswap: myswap: warning: wiping old swap signature.
Setting up swapspace version 1, size = 1000 MiB (1048571904 bytes)
no label, UUID=a336af9e-85db-4caa-8506-4c27cb881a7e
[excellent@ip-172-31-56-105 backups]$ sudo swapon myswap
[excellent@ip-172-31-56-105 backups]$ free -m
               total        used        free      shared  buff/cache   available
Mem:             963         207         117           2         638         595
Swap:            999           0         999
```

<h4>Configure a Partition/Logical Volume to use swap</h4>
<h5>Using Partiton</h5>
* Attach a new disk
* Use `fdisk` utility to access disk
  - `sudo fdisk /dev/<disk_name>`
* Make and modify Partition
  - Press `p` to list current partition
  - Press `n` to make a new partition
    * Fill out sectors: refer to [2023-05-17-fdisk](2023-05-17-fdisk.md) 
  - Press `t` to specify a type
    * Press `L` to list all types
    * Specify a partition
  - Press `19` to set type to Linux Swap
* Make the Partition into a swap file
  - `sudo mkswap /dev/xvdf`
    * Since you are using the whole partition as the swapfile, theres no need to use `dd` to create a swap file
* Enable the swap area
  - `sudo swapon /dev/xvdf`
  - `free -h` or use `sudo swapon --show`
* NOTE: you do not need to mount swap areas
<h5>Using LVM</h5>
* Attach a new disk
* Convert disk into a PV (or you can create a partition from the disk to use as a PV)
  - `sudo pvcreate /dev/<disk_name>`
* Add the PV into a VG
  - `sudo vgcreate myvg0 /dev/<disk_name>`
* Create a LV
  - `sudo lvcreate -n lv3 -L 500M <VG_name>`
* Convert into a swap area
  - `sudo mkswap /dev/mapper/myvg0-lv3`
  - `free -h` to confirm
* For more information on LVM refer to [2023-05-23-lvm-logical-volumes](2023-05-23-lvm-logical-volumes.md)
<h3>Disable swapfile or a swap area</h3>
* Make sure that the swap space isnt in use
* Disable Swap
  - `sudo swapoff -a` (disables all swap) or `sudo swapoff /backups/myswap`
  * <h5>Extending</h5>
    * Use `dd` command to extend swapfile with new size
    - `sudo dd if=/dev/zero of=/path/to/swap bs=<new-sze> count=<blocks>` or just increase partition or volume if that the case
    * Set up the extended swap
    - `sudo mkswap /path/to/swap`
    * Enable swap
    - `sudo swapon /path/to/swap`
    - `free -h`
