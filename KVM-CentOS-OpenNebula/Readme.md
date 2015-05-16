### INTRODUCTION:

For a large insfrastructure, you need a shared storage on network for KVM hosts. You can use FreeNaas for storage creation. FreeNaas is opensource. Now, i am going to show you multiple KVM hosts in shared storage. You can use openNebula for connecting more KVM hosts and for geeting more interactive and enhanced GUI.

#### KVM host Setup:

```
yum -y install @virt* dejavu-lgc-* xorg-x11-xauth tigervnc libguestfs-tools policycoreutils-python bridge-utils

## Allow packet forwarding between interfaces:

sed -i 's/^\(net.ipv4.ip_forward =\).*/\1 1/' /etc/sysctl.conf; sysctl -p 

## Configure libvirtd service to start automatically and reboot:
chkconfig libvirtd on; reboot

## you can set up bridging which will allow guests to have a network adaptor on the same physical lan as the host. 
In this example eth0 is the device to support the bridge and br0 will be the new device.

chkconfig network on
service network restart
yum -y erase NetworkManager
cp -p /etc/sysconfig/network-scripts/ifcfg-{eth0,br0}
sed -i -e'/HWADDR/d' -e'/UUID/d' -e's/eth0/br0/' -e's/Ethernet/Bridge/' \
/etc/sysconfig/network-scripts/ifcfg-br0
echo DELAY=0 >> /etc/sysconfig/network-scripts/ifcfg-br0
echo 'BOOTPROTO="none"' >> /etc/sysconfig/network-scripts/ifcfg-eth0
echo BRIDGE=br0 >> /etc/sysconfig/network-scripts/ifcfg-eth0
service network restart
brctl show
```
#### Install Vms:

```
virt-install --name centos-15 --ram=1024 --vcpus=2 --network bridge=br0 --os-variant=rhel6 \
--force --disk path=/home/centos.img,size=10 -l http://mirror.centos.org/centos/6/os/x86_64 --nographics

Now your vm with name centos-15 is ready and running.
```
You can see all the options of virt-install and use it. For managing Vm's such as migration, cloninng, etc, you can use virsh in CLI. But for GUI managemnet you will need virt-manager and for that you will need desktop version of server and for managing that KVM remotely VNC should be installed on that server. Now I m going to show you how to setup VNC server:

```
yum install vnc-server vnc

yum groupinstall Desktop

yum install xterm
```
#### Now, you have to follow below steps:

1.Create the VNC users accounts.
```
useradd username
passwd username
```
2.Edit the server configuration.
```
Edit /etc/sysconfig/vncservers and add the following to the end of the file.
VNCSERVERS="1:username"
VNCSERVERARGS[1]="-geometry 640x480"
```
3.Set your users' VNC passwords.
```
su - larry
vncpasswd
```
4.Confirm that the vncserver will start and stop cleanly.

```
service vncserver restart
```
5.Stop the iptables.
6. Login through desktop on your vnc server and enable desktop sharing and go to your workstation which may be any windows , mac or ubuntu mavhine, vnc client should be installed on your machine. Access desktop of your server and use virt-manager from desktop to create ,delete, clone migrate the vms from one server to another server.

### OpenNebula Server:

After following this guide, users will have a working OpenNebula with graphical interface (Sunstone), at least one hypervisor (host) and a running virtual machines.Throughout the installation there are two separate roles: Frontend and Nodes. The Frontend server will execute the OpenNebula services, and the Nodes will be used to execute virtual machines. Please not that it is possible to follow this guide with just one host combining both the Frontend and Nodes roles in a single server. However it is recommended execute virtual machines in hosts with virtualization extensions. To test if your host supports virtualization extensions, please run:
```
grep -E 'svm|vmx' /proc/cpuinfo
```
Disable your selinux:

```
setenforce 0
```

#### Now folllow below steps to install opennebula:
```
### Add epel repository:

yum install epel-release

### Add the OpenNebula Repo:

cat << EOT > /etc/yum.repos.d/opennebula.repo
[opennebula]
name=opennebula
baseurl=http://downloads.opennebula.org/repo/4.10/CentOS/6/x86_64/
enabled=1
gpgcheck=0
EOT

### Install the required packages

yum install opennebula-server opennebula-sunstone

### Now run install_gems to install all the gem dependencies. Choose the CentOS/RedHat if prompted:

/usr/share/one/install_gems

### Configure and Start the services:

There are two main processes that must be started, the main OpenNebula daemon: oned, and the graphical user interface: sunstone
Sunstone listens only in the loopback interface by default for security reasons. To change it edit /etc/one/sunstone-server.conf 
and change :host: 127.0.0.1 to :host: 0.0.0.0.

###Now we can start the services

service opennebula start
service opennebula-sunstone start

If there is any ruby dependecy error figure it out at your own.

### Configure NFS:

Skip this section if you are using a single server for both the frontend and worker node roles.
Export /var/lib/one/ from the frontend to the worker nodes. To do so add the following to the /etc/exports file in the frontend:
/var/lib/one/ *(rw,sync,no_subtree_check,root_squash)

Refresh the NFS exports by doing:

service rpcbind restart
service nfs restart

### Configure SSH Public Key:
OpenNebula will need to SSH passwordlessly from any node (including the frontend) to any other node.
Add the following snippet to ~/.ssh/config as oneadmin so it doesn’t prompt to add the keys to the known_hosts file:

su - oneadmin
$ cat << EOT > ~/.ssh/config
Host *
    StrictHostKeyChecking no
    UserKnownHostsFile /dev/null
EOT
$ chmod 600 ~/.ssh/config
```
### Installation in the Nodes:
```
Add the OpenNebula repository here also.

yum install opennebula-node-kvm ##  Install the required packages

### Start the required services:

service messagebus start
service libvirtd start

### configure network bridging same as you have done before for KVM hosts.

### Configure NFS:
Skip this section if you are using a single server for both the frontend and worker node roles.

Mount the datastores export. Add the following to your /etc/fstab:
192.168.1.1:/var/lib/one/  /var/lib/one/  nfs   soft,intr,rsize=8192,wsize=8192,noauto

Replace 192.168.1.1 with the IP of the frontend.

### Mount the NFS share:

mount /var/lib/one/

```

Now you can use access frontend by accessing following url through your browser:

http://frontendIp:9869.

The default password for the oneadmin user can be found in ~/.one/one_auth which is randomly generated on every installation.

Now, you can make your own vCloud, vDatatcenter and you can make templates, migrate, clone, and autoscale VMs. Use "oneflow" server with opennebula for more functionality.

