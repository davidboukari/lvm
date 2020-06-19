# lvm


## Example
physical disk1 
physical disk2

groupe1 = physical disk1  + physical disk1 

lv1 is created on groupe1
lv2 is created on groupe1

## commands

```bash
pvs
vgs
lvs
```

# Increase or decrease disk size
```bash
pvresize --setphysicalvolumesize 14G /dev/vda5

lvextend -L 20G /dev/groupe1/lv1
lvextend  -l  +200 /dev/mapper/vgappdata-lvappdata

# resize the FS ext4
resize2fs /dev/groupe1/lv1

# resize the FS xfs
xfs_growfs 
```
