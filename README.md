# lvm

##
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
vgextend /dev/sda3 centos
vgextend centos /dev/sda3
lvm lvextend -L+5G /dev/mapper/centos-root
# if ext4 resize2fs of xfs_grow if xfs
resize2fs  /dev/centos/root
or
xfs_growfs /dev/centos/root

```

## Increase or decrease disk size
```bash
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
