<h1>Disk Partitioning</h1>
Disk Partitioning: creating a logical region within a hard disk/storage.
* Partition: a logical division of a storage device
  * `fdisk -l`: lists all partitions on a server
  * `lsblk`: lists all disks and partitions in each disk
* File System: a method used to organize and manage files within a partition
  * `df -h`: lists all file systems on a server
  * `df -T`: lists all file systems as well as the file system types on the server
  - Types of File Systems:
    - ext4 (Fourth Extended File System): default file system, enhanced version of ext3 file system, includes journaling (maintains a journal or log of changes before they are commited to the fs)
    - xfs: high-performance file system that handles large files, good for snapshotting and online resizing
    - ext3: earlier version of ext4
    - tmpfs: is a temporary file system, resides on the RAM (memory) rather than on the hard disk, since its on memory it provides fast read and write access. However it is on RAM, so if server reboots then all data stored is lost. Mainly used for temporary files, caches, and other data
    - devtmpfs: is a type of tmpfs designed for managing device files, this type of tmpfs is persistent across system reboots. Responsible for populating the `/dev` with appropriate device nodes during a system reboot. 
    - vfat (Virtual File Allocation Table): mainly used in Windows OS to manage files on storage devices like hard drives and USD. In linux it allows systems to rw to VFAT-formated storage devices, allows access to Windows-compatible devices
<h3>Creating a partition</h3>
* Attach a new disk to the server
  - This can be done in AWS, by attaching a new EBS volume
* Scan the server for new hardware changes
* Create the new disk in the server
* Make the filesystem, format the drive (the volume)
* Mount the new partition
<h5>Test this out with an AWS EBS volume</h5>
Note: it is important to have backups of all paritions
* Modify existing volume, or add another disk drive to the EC2 instance
* Run `sudo fdisk -l` to list all available disks and their partitions
  - Find the volume you modified
* Run `sudo fdisk /dev/<disk_name>` to start the `fdisk` utility
  - Press `d` to delete the existing partition(s)
  - Press `p` to print the current partition table
  - Press `n` to create a new partition
    * if you want to use the entire disk as a single partition press `p` for primary parition, then press `1` for the parition number, and accept the default values
  - Press `w` to write the changes to the disk and exit the `fdisk` utility
* After this, the partition table should be updated to reflect the EBS size change
  - But, the fs inside the partition might not be aware of the size changes yet
<h5>Mounting a disk(volume) to a server</h5>
* Create and attach the drive (EBS) to the server
* Run `lsblk` or `fdisk -l` to list the new disk drive
* Create a fs from that disk 
  - `sudo mkfs.ext4 /dev/xvdf`: you can choose any fs that suits your needs
    * Note: you can only create one fs per partition
* Mount the volume to a path: `sudo mount -t ext4 /dev/xvdf /backups`
<h5>Creating a partition on a disk drive</h5>
* Run `sudo fdisk /dev/disk_name` to use the fdisk utility
* Create a new partition using `n`: at which point you'll have the option to choose `p` for primary partition or `e` for extended partition
  - Primary Partion: a type of partition that can be directly bootable, a standalone partition that exisits within the master boot record (MBR)
  - Extended Partition: a special parition that servers as a container for logical paritions, it used when you want to create more than 4 partitions on a disk using the MBR partitioning scheme, it does not contain any data itself, but acts as a wrapper for logial partitions within it. 
    * You can only have one extended partition on a disk
    * You can create logical partitions within that extended partition (allows for more partitions instead of just the default 4)
* Select `p` for primary partition
  - First sector select where you want the partition to begin on the disk (use default in most cases, linux will choose next available first sector
  - Last sector: select where you want the partition to end on the disk (you can use values and sizes)
    * ex: `+2G for 2 Gigabytes`, or select exactly which byte you want it to end at
* Create a fs on the partition
  - `sudo mkfs.ext4 /dev/xvdfa`
* Mount the volume to a path: `sudo mount -t ext4 /dev/xvdfa /backups`
<h5>Expanding an exisiting disk</h5>
* Modify the existing drive(volume)
* Run `sudo fdisk -l` to list all volumes, this should also show a discrepency within the disk space size

```
[excellent@ip-172-31-56-105 eoyebami.github.io]$ sudo fdisk -l
GPT PMBR size mismatch (16777215 != 20971519) will be corrected by write.
The backup GPT table is not on the end of the device.
Disk /dev/xvda: 10 GiB, 10737418240 bytes, 20971520 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: F055A840-0325-402E-B88B-DCF28B362703

Device       Start      End  Sectors Size Type
/dev/xvda1   24576 16777182 16752607   8G Linux filesystem
/dev/xvda127 22528    24575     2048   1M BIOS boot
/dev/xvda128  2048    22527    20480  10M EFI System

Partition table entries are not in disk order.
```

* Correct discrepency by running a write on the disk
  - `sudo fdisk /dev/xvda`: Note: you are modifying the disk drive not the parition `(/dev/xvda1)`
  - find disk by running `lsblk`
  - run `w` (write) to update the disk size to reflect volume change
<h5>Resizing an exisiting Partition</h5>
* Umount the partition you'd like to resize
  - `sudo unmount /dev/<disk_name>`
  - As well as shut down all applications that are using the partitions
* Run `fdisk /dev/<disk_name>` to use fdisk utility 
* Delete the partition you want to extend `d`
  - select which partition you'd like to delete
* Run `n` to create a new partition
  - recreate this partition with an increased last sector
  - the filesystem that was on th deleted partition will be reassigned to the new partition (because it will have the same name)
* Run `w` to write the changes to the disk 
* Run `resize2fs /dev/<partition_name>` 
  - You can also `reszie2fs -p /dev/<partition_name> 1G` to increase it by 1G instead of the maximum set by the fdisk utility
<h3>Deleting a partition</h3>
* Run final archive backup, to backup all data
* Bring down all applications hosted on that partition
* Unmount the mount point of that partition
  - `sudo umount /dev/<disk_name>`: will unmount the partition from the server
* Delete partition using fdisk
  - use `sudo fdisk /dev/<disk_name>` to access fdisk utility
  - press `d` and you will be promoted to choose which partition you'd like to delete
* Destroy the HDD(hard drive or the volume) 
  - Detach volume and delete it
