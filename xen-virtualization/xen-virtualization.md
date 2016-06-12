###Virtualization:
There are two common approaches to virtualization - full virtualization and para-virtualization. Full virtualization provides complete abstraction between the hardware and the guest operating system. In this scenario, the guest operating system is provided a complete virtual physical environment in which to run and, as such, is unaware that it is running inside a virtual machine. One advantage of full virtualization is that the operating system does not need to be modified in order to run in a virtualized environment. This means that proprietary operating systems such as Windows can be run on Linux systems. Disadvantages of full virtualization are that performance is slightly reduced as compared to para-virtualization, and some virtualization platforms, such as Xen and KVM, require CPUs with special virtualization support built in (such as Intel-VT and AMD-V).

Para-virtualization requires that a guest operating system be modified to support virtualization. This typically means that guest operating systems are limited to open source systems such as Linux. It is also not possible to migrate a running guest OS from one server to another. The advantage to this approach, however, is that a para-virtualized guest system comes closer to native performance than a fully virtualized guest, and the latest virtualization CPU support is not needed.

####Steps to Install and configure xen :
```
yum install centos-release-xen

yum groupinstall 'Virtualization'

yum install xen

```
1. Configure network bridge for first VM:
A network bridge is a Link Layer device which forwards traffic between networks based on MAC addresses and is therefore also referred to as a Layer 2 device. It makes forwarding decisions based on tables of MAC addresses which it builds by learning what hosts are connected to each network. A software bridge can be used within a Linux host in order to emulate a hardware bridge, for example in virtualization applications for sharing a NIC with one or more virtual NICs. 

Create a file  ifcfg-br0 with following content :
```
DEVICE=br0
TYPE=Bridge
IPADDR=x.x.x.x
NETMASK=x.x.x.x
ONBOOT=yes
BOOTPROTO=none
NM_CONTROLLED=no
DELAY=0
```

Make folowing changes in ifcfg.eth0 :
```
DEVICE=ethX
TYPE=Ethernet
HWADDR=AA:BB:CC:DD:EE:FF
BOOTPROTO=none
ONBOOT=yes
NM_CONTROLLED=no
BRIDGE=br0
```
Now you can use various ways to bring up your first VM like virt-manager, xm and libvirt :
virt-manager provide you gui to provide all operations on vm
#### xm command list :
```
xm help [--long]: view available options and help text.
use the xm list command to list active domains:
$ xm list
Name                                ID  Mem(MiB)   VCPUs      State      Time(s)
Domain-0                            0     520       2         r-----     1275.5
r5b2-mySQL01                       13     500       1         -b----       16.1
xm create [-c] DomainName/ID: start a virtual machine. If the -c option is used, the start up process will attach to the guest's console.
xm console DomainName/ID: attach to a virtual machine's console.
xm destroy DomainName/ID: terminate a virtual machine , similar to a power off.
xm reboot DomainName/ID: reboot a virtual machine, runs through the normal system shut down and start up process.
xm shutdown DomainName/ID: shut down a virtual machine, runs a normal system shut down procedure.
xm pause
xm unpause
xm save
xm restore
xm migrate

Resource management options

Use the following xm commands to manage resources:

xm mem-set
use the xm vcpu-list to list virtual CPU assignments/placements:
$ xm vcpu-list
Name                          ID  VCPUs   CPU State  Time(s)  CPU Affinity
Domain-0                       0    0      0    r--   708.9    any cpu
Domain-0                       0    1      1   -b-    572.1    any cpu
r5b2-mySQL01                  13    0      1   -b-     16.1    any cpu
xm vcpu-pin
xm vcpu-set
use the xm sched-credit command to display scheduler parameters for a given domain:
$ xm sched-credit -d 0
{'cap': 0, 'weight': 256}
$ xm sched-credit -d 13
{'cap': 25, 'weight': 256}

Monitoring and troubleshooting options

Use the following xm commands for monitoring and troubleshooting:

xm top
xm dmesg
xm info
xm log
use the xm uptime to display the uptime of guests and hosts:
$ xm uptime
Name                       ID  Uptime
Domain-0                    0  3:42:18
r5b2-mySQL01               13  0:06:27
xm sysrq
xm dump-core
xm rename
xm domid
xm domname
```


