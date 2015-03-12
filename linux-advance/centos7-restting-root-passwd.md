#### Resetting the Root Password Using rd.break:

1.Start the system and, on the GRUB 2 boot screen, press the e key for edit and then press a key.

2.Remove the rhgb and quiet parameters from the end, or near the end.

3.Add the following parameters at the end of the linux line and press enter to boot the system:
```
rd.break enforcing=0
```
Adding the enforcing=0 option enables omitting the time consuming SELinux relabeling process.

The initramfs will stop before passing control to the Linux kernel, enabling you to work with the root file system.

Note that the initramfs prompt will appear on the last console specified on the Linux line.

The initramfs switch_root prompt appears.

4.The file system is mounted read-only on /sysroot/. You will not be allowed to change the password if the file system is not writable.

Remount the file system as writable:
```
mount -o remount,rw /sysroot
```
5.Change the file system's root as follows:
```
chroot /sysroot
```
The prompt changes to sh-4.2#.

6.Enter the passwd command and follow the instructions displayed on the command line to change the root password.

7.Updating the password file results in a file with the incorrect SELinux security context. To relabel all files on next system boot, enter the following command:
```
touch /.autorelabel
```
8.Alternatively, to save the time it takes to relabel a large disk, you can omit this step provided you included the enforcing=0 option in step 3.

8.Remount the file system as read only:
```
mount -o remount,ro /
```
9.Enter the exit command two times to exit the chroot environment and boot the system.

#### NOTE: Now, after rebooting you will be able to login with new password.

Note that the SELinux relabeling process can take a long time. A system reboot will occur automatically when the process is complete.

10. If you added the enforcing=0 option in step 3 and omitted the touch /.autorelabel command in step 8, enter the following command to restore the /etc/shadow file's SELinux security context:
```
restorcon /etc/shadow
```

