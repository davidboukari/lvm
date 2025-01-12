# lvm

## To detect the LVM
Need Kernel Device mapper support raid module
```bash
lsmod | grep dm_mod
echo "dm-mod" >> /etc/modprobe.preload
vgscan
```


# vgscan

## Example
physical disk1 
physical disk2

groupe1 = physical disk1  + physical disk1 

lv1 is created on groupe1
lv2 is created on groupe1

## commands

```bash
pvdisplay -v 
vgdisplay -v
lvdisplay -v
pvs
vgs
lvs
```

# Add a disk or partition
* https://www.cyberciti.biz/faq/howto-add-disk-to-lvm-volume-on-linux-to-increase-size-of-pool/
```bash
fdisk /dev/sdb 
# create the partition

parprobe /dev/sdb
cat /proc/partition

pvdisplay -v
pvcreate /dev/sda1
lvmdiskscan -l
vgdisplay -v

# Add pv in a vg 
vgextend centos /dev/sda3

# Create an LV
lvcreate -L 10G -n lvtest /dev/vg

# Extend a LV
lvextend -L +5G /dev/mapper/centos-root

# Decrease an LV
lvextend -L -5G /dev/mapper/centos-root

# Set directly a size for an LV
lvextend -L 15G /dev/mapper/centos-root

# Increase  +15G a size for an LV
lvextend -L +15G /dev/mapper/centos-root


# Set 100%   a size for an LV
lvextend -l +100%FREE /dev/ubuntu-vg/ubuntu-lv
resize2fs /dev/ubuntu-vg/ubuntu-lv 



# if ext4 resize2fs of xfs_grow if xfs
resize2fs  /dev/centos/root
or
xfs_growfs /dev/centos/root

```

## Create a logical volume
```
lvcreate -L 10G -n lvtest /dev/vg
  WARNING: PV /dev/sda3 in VG vg is using an old PV header, modify the VG to update.
  WARNING: updating PV header on /dev/sda3 for VG vg.
  Logical volume "lvtest" created.

```

## Add new disk to as ps add to lvmgroup and extend a pv
* https://www.cyberciti.biz/faq/howto-add-disk-to-lvm-volume-on-linux-to-increase-size-of-pool/
```
* New 
fdisk /dev/sdb
n: primary 
p: type 8e

# Create pv
pvcreate /dev/sdb

# Add pv to group
vgextend cl /dev/sdb

# Extend the lv
lvextend -L+5G  /dev/cl/root

# if ext4 resize2fs of xfs_grow if xfs
resize2fs  /dev/centos/root
or
xfs_growfs /dev/centos/root
```


## Increase or decrease disk size
```bash
# resize 100% the physical volume
pvresize --setphysicalvolumesize /dev/vda5

# Increase the physical volume
pvresize --setphysicalvolumesize 14G /dev/vda5

# Increase the logical volume
lvextend -L 20G /dev/mapper/vgappdata-lvappdata
# resize the FS ext4
resize2fs /dev/groupe1/lv1

# resize the FS xfs
xfs_growfs 
```

```bash
pvresize --setphysicalvolumesize 14G /dev/vda5

lvextend -L 20G /dev/groupe1/lv1
lvextend  -l  +200 /dev/mapper/vgappdata-lvappdata

# resize the FS ext4
resize2fs /dev/groupe1/lv1

# resize the FS xfs
xfs_growfs 
```
## Increase physical volume and logical disk
* https://computingforgeeks.com/how-to-extend-increase-kvm-virtual-machine-disk-size/
```
$ df -h
Filesystem                    Size  Used Avail Use% Mounted on
udev                           16G     0   16G   0% /dev
tmpfs                         3.2G   13M  3.2G   1% /run
/dev/mapper/eve--ng--vg-root   78G   49G   25G  67% /
tmpfs                          16G     0   16G   0% /dev/shm
tmpfs                         5.0M     0  5.0M   0% /run/lock
tmpfs                          16G     0   16G   0% /sys/fs/cgroup
tmpfs                         3.2G     0  3.2G   0% /run/user/0

# Get the ID of the VM by checking it in edit mode in the LAB
$ cd /opt/unetlab/tmp/0/405d54df-5790-4b61-b65b-af5a09ed80c9/6

$ qemu-img info hda.qcow2
image: hda.qcow2
file format: qcow2
virtual size: 10G (10737418240 bytes)
disk size: 9.2G
cluster_size: 65536

# qemu resize the disk
$ qemu-img resize /opt/unetlab/tmp/0/405d54df-5790-4b61-b65b-af5a09ed80c9/6/hda.qcow2 +10G    
    
$ qemu-img info /opt/unetlab/tmp/0/405d54df-5790-4b61-b65b-af5a09ed80c9/6/hda.qcow2
image: /opt/unetlab/tmp/0/405d54df-5790-4b61-b65b-af5a09ed80c9/6/hda.qcow2
file format: qcow2
virtual size: 20G (21474836480 bytes)
disk size: 9.2G
    
# Start the VM 
# Destroy the partition and recreate-it to the good size
fdisk /dev/sda
d 2
n
2
partprobe

# extend the physical volume
pvresize /dev/sda2
  Physical volume "/dev/sda2" changed
  1 physical volume(s) resized or updated / 0 physical volume(s) not resized
[root@localhost centos]# pvdisplay -v
  --- Physical volume ---
  PV Name               /dev/sda2
  VG Name               centos
  PV Size               <19,00 GiB / not usable 2,00 MiB
  Allocatable           yes
  PE Size               4,00 MiB
  Total PE              4863
  Free PE               2560
  Allocated PE          2303
  PV UUID               bMpCuC-kC2V-qVBa-hvzc-Fj5T-LW3R-a3dOnB

# extend the logical volume
lvextend -L +8G /dev/centos/root
  Size of logical volume centos/root changed from <8,00 GiB (2047 extents) to <16,00 GiB (4095 extents).
  Logical volume centos/root successfully resized.
  
# Extented the FS resize2fs of xfs_grow
xfs_growfs /dev/centos/root
meta-data=/dev/mapper/centos-root isize=512    agcount=4, agsize=524032 blks
         =                       sectsz=512   attr=2, projid32bit=1
         =                       crc=1        finobt=0 spinodes=0
data     =                       bsize=4096   blocks=2096128, imaxpct=25
         =                       sunit=0      swidth=0 blks
naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
log      =internal               bsize=4096   blocks=2560, version=2
         =                       sectsz=512   sunit=0 blks, lazy-count=1
realtime =none                   extsz=4096   blocks=0, rtextents=0
data blocks changed from 2096128 to 4193280

[root@localhost centos]# lvdisplay /dev/centos/root
  --- Logical volume ---
  LV Path                /dev/centos/root
  LV Name                root
  VG Name                centos
  LV UUID                fceusN-nEsI-kcf4-wWrY-XUdA-ZXbs-O0Qn7a
  LV Write Access        read/write
  LV Creation host, time localhost, 2021-02-22 21:20:39 +0100
  LV Status              available
  # open                 1
  LV Size                <16,00 GiB
  Current LE             4095
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     8192
  Block device           253:0

```
