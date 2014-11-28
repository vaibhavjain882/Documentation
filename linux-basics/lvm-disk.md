### Adding LVM Disk

Add a disk to the machine and -

Create new partition :

```
fdisk -cu /dev/disk_name
```

Add the disk to your machine as a primary partition. you can add lvm as extendedpartition but it doesn't make any sense because lvm as primary partition is more
manageable than any other extended partition. And basically lvm partition remove
our dependency on extended partition and provide more flexibility in terms of
manageability (increasing/decreasing the size of entire disk, addition/deletion
of other disks etc.).

Now a command prompt appears. Press n and enter then press p and enter then choose primary partition number from 1 to 4 and enter. Now for first cylinder just press enter then specify the size by adding +"size" for last cylinder and enter then press t and enter then press 8e (for lvm) and enter then press w and enter.

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
mkfs.ext4 /dev/mapper/volume_group_name-logical_volume_name
mount /dev/mapper/volume_group_name-logical_volume_name /mountpoint

### Mount Point should be created.

## Now, make an entry in /etc/fstab file so that it will be mounted after reboot.

/dev/mapper/volume_group_name-logical_volume_name /mountpoint ext4 deafults 0 0
```

 





