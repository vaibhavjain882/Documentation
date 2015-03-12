###Upgrade CentOS 6.5 to 7.0 using preupg

####CentOS 7 Major Changes

```
Following are the more notable changes are included in this release are:
1.Updated Kernel to 3.10.0

2.Added support for Linux Containers

3.Open VMware Tools & 3D graphics drivers out of the box

4.OpenJDK-7 as default JDK

5.Upgrade from 6.5 to 7.0 using preupg command

6.LVM-snapshots with ext4 and XFS

7.Switch to grub2, systemd and firewalld

8.Default XFS file system

9.iSCSI and FCoE in kernel space

10.Support for PTPv2

11.Support for 40G Ethernet Cards

12.Supports installations in UEFI (Unified Extensible Firmware Interface) Secure Boot form 
on compatible hardware
```
####Many of things have been changed in this release.:

```
1.grub is now replaced with grub2

2.init is now replaced with systemd

3.Difficultly in understanding and editing grub.conf (grub2)

4.Difficultly in understanding /etc/init.d

5.No more text log files for system log (journalctl instead)

6.No more ext4 filesystem, added XFS as default filesystem

7.CentOS 6.x will be supported until 2020
```
#### upgrade our existing CentOS 6.5 to 7.0 using CentOS Upgrade tools.

The Preupgrade Assistant (preupg) checks for potential problems you might encounter with an upgrade from Red Hat Enterprise Linux 6 to Red Hat Enterprise Linux 7 before making any changes to your system. This helps you assess your chances of successfully upgrading to Red Hat Enterprise Linux 7 before the actual upgrade process begins.
The Preupgrade Assistant assesses the system for possible in-place upgrade limitations, such as package removals, incompatible obsoletes, name changes, deficiencies in some configuration file compatibilities, and so on. It then provides the following:

System analysis report with proposed solutions for any detected migration issues.

Data that could be used for “cloning” the system, if the in-place upgrade is not suitable.

Post-upgrade scripts to finish more complex issues after the in-place upgrade.

Your system remains unchanged except for the information and logs stored by the Preupgrade Assistant.

```
yum -y update

reboot

yum -y install openscap pcre-devel libxml2-devel libxslt-devel m2crypto python-simplejson mod_wsgi

rpm -ihv http://dev.centos.org/centos/6/upg/x86_64/Packages/preupgrade-assistant-1.0.2-33.el6.x86_64.rpm

rpm -ihv http://dev.centos.org/centos/6/upg/x86_64/Packages/preupgrade-assistant-contents-0.5.13-1.el6.noarch.rpm

rpm -ihv http://dev.centos.org/centos/6/upg/x86_64/Packages/preupgrade-assistant-ui-1.0.2-33.el6.x86_64.rpm

rpm -ihv http://dev.centos.org/centos/6/upg/x86_64/Packages/python-rhsm-1.9.7-1.el6.x86_64.rpm

rpm -ihv http://dev.centos.org/centos/6/upg/x86_64/Packages/redhat-upgrade-tool-0.7.22-1.el6.noarch.rpm

preupg -s RHEL6_7		

#From the above you can find what all are the packages and application will be affected by 
this upgrade, if you are ok with it; you can go ahead for the next step.You will find your all 
the recommendation in "/root/preupgrade/./" directory

redhat-upgrade-tool-cli --network 7.0 --instrepo http://mirror.centos.org/centos/7/os/x86_64/ --force

reboot

cat /etc/redhat-release
```
####Above way is only for upgrading centos 6.5. If you want general way then just follow below steps:

```
vi /etc/yum.repos.d/upgrade.repo

[upgrade]
name=upgrade
baseurl=http://dev.centos.org/centos/6/upg/x86_64/
enabled=1
gpgcheck=0

yum -y install preupgrade-assistant-contents redhat-upgrade-tool preupgrade-assistant

preupg

rpm --import http://centos.excellmedia.net/7.0.1406/os/x86_64/RPM-GPG-KEY-CentOS-7

 redhat-upgrade-tool --network 7.0 --instrepo http://centos.excellmedia.net/7.0.1406/os/x86_64/ 

# In between the upgradation , you will find the list of issues if you are okay with it
then continue the upgradation otherwise first fix the issue and then again run the command.
```



