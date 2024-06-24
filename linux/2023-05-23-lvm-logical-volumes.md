<h1>Using Logical Volume Manager Partitions</h1>
 
LVM is used when dealing with changing storage needs. With LVM, disk partitions are added to pools of space called volume groups. Logical volumes are assigned space from those volume groups, which allows:
* Addition of more space to a logical volume from the volume group while the volume is still in use 
* Add more physical volumes to a volume group
* Move data from one physical volume to another so you can remove smaller disks and replace them with larger ones while the fs is still in use
<h3>Working with LVM</h3>
 
* First you need to down the dependencies for LVM
  - `sudo yum install lvm2-2.03.16-1.amzn2023.0.4.x86_6`
* If a partition is type lvm, then you can use the `pvdisplay` command to display information about that physical volume
  - Use `pvcreate /dev/<disk_partition>` to make a partition into a lvm physical volume (PV)

   ```console
   $ sudo pvdisplay /dev/xvdf2
     "/dev/sdf2" is a new physical volume of "1.00 GiB"
     --- NEW Physical volume ---
     PV Name               /dev/sdf2
     VG Name
     PV Size               1.00 GiB
     Allocatable           NO
     PE Size               0   
     Total PE              0
     Free PE               0
     Allocated PE          0
     PV UUID               sNWHWD-QEyj-58YI-itCu-eJ0t-SgNi-7Sxwce
   ```

* Create the volume group (VG) using the PV
  - `sudo vgcreate myvg0 /dev/xvdf2`
  - The `pvdisplay` output should now be different 
  - Ex:

   ```console
   $ sudo vgcreate myvg0 /dev/xvdf2
     Volume group "myvg0" successfully created
   $ sudo pvdisplay /dev/xvdf2
     --- Physical volume ---
     PV Name               /dev/sdf2
     VG Name               myvg0
     PV Size               1.00 GiB / not usable 4.00 MiB
     Allocatable           yes
     PE Size               4.00 MiB
     Total PE              255
     Free PE               255
     Allocated PE          0
     PV UUID               sNWHWD-QEyj-58YI-itCu-eJ0t-SgNi-7Sxwce
   ```

* Run `vgdisplay myvg0` to display information about that VG
  * Ex:

   ```console
   $ sudo vgdisplay myvg0
     --- Volume group ---
     VG Name               myvg0
     System ID
     Format                lvm2
     Metadata Areas        1
     Metadata Sequence No  1
     VG Access             read/write (can be made to read only: vgchange -ro <vg_name>)
     VG Status             resizable  (can be made non resizable: vgchange --resizeable n <vg_name>)
     MAX LV                0
     Cur LV                0
     Open LV               0
     Max PV                0
     Cur PV                1
     Act PV                1
     VG Size               1020.00 MiB
     PE Size               4.00 MiB (Physical Extent: the smallest unit of allocation within a VG)
     Total PE              255
     Alloc PE / Size       0 / 0
     Free  PE / Size       255 / 1020.00 MiB
     VG UUID               0Kwf46-ADIg-OvNk-aQ3U-IQz9-Hpo9-hmsNFF
   ```

* Create a logical volume (LV) from that VG
  - `lvcreate -n lv1 -L 100M myvg0`: creates a LV called `lvl` with `100M` from VG `myvg0`
  - locate the new LV in `/dev/mapper/myvg0-<LV-name>`
  - run `lvdisplay <VG_name>` or `lsblk` to see the new logical volume
  * Ex:

   ```console
   $ sudo lvcreate -n lv1 -L 100M myvg0
     Logical volume "lv1" created.
   $ lsblk
   NAME          MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
   xvda          202:0    0   10G  0 disk
   ├─xvda1       202:1    0    8G  0 part /
   ├─xvda127     259:0    0    1M  0 part
   └─xvda128     259:1    0   10M  0 part /boot/efi
   xvdf          202:80   0    8G  0 disk
   ├─xvdf1       202:81   0  5.6G  0 part /backups
   └─xvdf2       202:82   0    1G  0 part
     └─myvg0-lv1 253:0    0  100M  0 lvm
   $ sudo ls /dev/mapper/myvg0-*
   /dev/mapper/myvg0-lv1
   ```

* Create a fs in that logical volume and the mount it 
  - `mkfs.ext4 /dev/mapper/myvg0-<LV_name>`
  - `mount /dev/mapper/myvg0-<LV_name> /backups`
  - `df -Th` or `sudo fdisk -l`; the logical volume will appear as its own disk
* You can then mount permanently in `/etc/fstab` directory refer to [2023-05-22-parted](2023-05-22-parted.md) for more context on this
<h3>Modifying Disk Space to a VG</h3>
 
<h5>Adding more Disk Space to a VG</h5>
 
* Create another partition using fdisk
  * Ex:
0
```console
xvdf          202:80   0    8G  0 disk
├─xvdf1       202:81   0  5.6G  0 part /backups
├─xvdf2       202:82   0    1G  0 part
│ └─myvg0-lv1 253:0    0  100M  0 lvm
└─xvdf3       202:83   0    1G  0 part
```

* Create another PV with `pvcreate /dev/xvdf3`
* Extend the VG size using this new PV
  - `vgextend <VG_name> /dev/<PV_name>`
  * Ex:

   ```console
   $ sudo vgextend myvg0 /dev/xvdf3
     Volume group "myvg0" successfully extended
   $ sudo vgdisplay myvg0
     --- Volume group ---
     VG Name               myvg0
     System ID
     Format                lvm2
     Metadata Areas        2
     Metadata Sequence No  3
     VG Access             read/write
     VG Status             resizable
     MAX LV                0
     Cur LV                1
     Open LV               0
     Max PV                0
     Cur PV                2
     Act PV                2
     VG Size               1.99 GiB
     PE Size               4.00 MiB
     Total PE              510
     Alloc PE / Size       25 / 100.00 MiB
     Free  PE / Size       485 / 1.89 GiB
     VG UUID               0Kwf46-ADIg-OvNk-aQ3U-IQz9-Hpo9-hmsNFF
   ```

<h5>Reducing Disk Space</h5>
 
* Resize the PV you created from the partition
  - First retrieve the PV name from the parition by running `pvdisplay /dev/<partition_name>`
* Resize the fs of the PV
  - `sudo resize2fs /dev/<PV_name> <new_size>`
* Run a resize of the PV itself to reflect the changes
  - `sudo pvresize --setphysicalvolumesize <new_size> /dev/<PV_name>`
<h3>Modifying Disk space to a LV</h3>
 
When resizing a LV, umount is not necessary, you can directly increase it by extending the LV from any avaliable space in the VG
* Run an extend on the LV
  - `lvextend -L +1G /dev/mapper/myvg0-lv1`
  - you an run a -1G to reduce the LV, but the LV must be `umount` before any shrinking an occur
    - run `sudo fsck -f /dev/mapper/mvg0-lv1` to check and repair the fs on the logical volume before shrinking size
    - `lvreduce -L -200M /dev/mapper/myvg0-lv1`
* Run a resize on the fs so the Kernel is aware of the changes
  - `resize2fs -p /dev/mapper/myvg0-lv1`
  * Ex:

   ```console
    $ sudo lvextend -L +200M /dev/mapper/myvg0-lv1
      Size of logical volume myvg0/lv1 changed from 100.00 MiB (25 extents) to 300.00 MiB (75 extents).
      Logical volume myvg0/lv1 successfully resized.
    $ lsblk
    NAME          MAJ:MIN RM  SIZE RO TYPE MOUNTPOINTS
    xvda          202:0    0   10G  0 disk
    ├─xvda1       202:1    0    8G  0 part /
    ├─xvda127     259:0    0    1M  0 part
    └─xvda128     259:1    0   10M  0 part /boot/efi
    xvdf          202:80   0    8G  0 disk
    ├─xvdf1       202:81   0  5.6G  0 part /backups
    ├─xvdf2       202:82   0    1G  0 part
    │ ├─myvg0-lv1 253:0    0  300M  0 lvm
    │ └─myvg0-lv2 253:1    0  200M  0 lvm
    └─xvdf3       202:83   0    1G  0 part
    $ sudo resize2fs /dev/mapper/myvg0-lv1
    resize2fs 1.46.5 (30-Dec-2021)
    Resizing the filesystem on /dev/mapper/myvg0-lv1 to 307200 (1k) blocks.
    The filesystem on /dev/mapper/myvg0-lv1 is now 307200 (1k) blocks long.
   ```

