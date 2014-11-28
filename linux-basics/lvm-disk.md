### Adding LVM Disk

Create new disk :

```
fdisk -cu /dev/disk_name
```

Add the disk to your machine as a primary partition. you can add lvm as extended 
partition but it doesn't make any sense because lvm as primary partition is more

manageable than any other extended partition. And basically lvm partition remove 
our dependency on extended partition and provide more flexibility in terms of 

manageability (increasing/decreasing the size of entire disk, addition/deletion 

of other disks etc.).

                Choose primary partition number from 1 to 4 and then partit

ion type "8e" (which is for lvm) and also specify the size of the disk.

Now, Create physical LVM volume on your disk:

```
pvcreate /dev/disk_name1
```

Create Volume group for this disk:

```
vgcreate volume-group-name /dev/disk_name1
```

Now Create logical volume within this volume group:

```
lvcreate -L 100G -n logical-volume-name volume-group-name 

### put size as u wish but obviously it can not be greater than the size of the disk.
```

You can see the physical volume, volume group and logical volume by running 

following commands:

```
pvdisplay
vgdisplay
lvdisplay
```

Create filesystem and mount it:

```
mkfs -t ext4 /dev/mapper/volume_group_name-logical_volume_name
mount /dev/mapper/volume_group_name-logical_volume_name /mountpoint

### Mount Point should be created.

## Now, make an entry in /proc/mount file so that it will be mounted after reboot.

/dev/mapper/volume_group_name-logical_volume_name /mountpoint ext4 rw,relatime,barrier=1,data=ordered 0 0
```

 





