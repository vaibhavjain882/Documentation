### route command

route command by default will show the details of the kernel routing table entires. Here is an example-

```
$ route
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
192.168.62.0     *               255.255.255.0   U     0      0        0 eth0

```

The above command shows that if the destination is within the network range 192.168.62.0 - 192.168.1.255 then the gateway is *, which is 0.0.0.0.

#### Adding a default gateway

```
route add default gw 192.168.62.150
```

It adds the 192.168.62.150 as your default gateway but after reboot it will flush out.

#### Reject routing to a particular network or host

```
route add -host 192.168.1.51 reject # for rejecting host

route add -net 192.168.1.0 netmask 255.255.255.0 reject   # for rejecting any entire network.

```

### A network architecture

Suppose there are two network subnet 192.168.50.* and 192.168.60.* respectively called A and B.

#### Make 192.168.60.* from 192.168.50.*

```
On each machine in 192.168.50.* network a default gateway will be added as below 
$ route add default gw 192.168.50.1

In Gateway add the following routing entry:

$ route add -net 192.168.60.0 netmask 255.255.255.0 gw 192.168.60.1

```

#### Do same for accessing 192.168.50.* from 192.168.60.* with 192.168.60.1 gateway in 192.168.60.* network.

#### Allow external world.

```
At both gateways of both subnets Run following command:

$ route add default gw "lease line ip"

```
Now when you try to access the internet (for example: ping google.com) from any of these machines (for example, from 192.168.3.2), it will redirect to lease line ip.

#### NOTE: Use - del at the place of -add to delete the entries.

#### These changes will not be persistent after system reboot. So commit these changes to the relevant files within your distribution.

```
#Change following file for persistent entry:

vi /etc/syscinfig/network

Gateway="GatewayIp"

# You can add additional static route for eth0, make a file in /etc/sysconfig/netwotk-scripts/ called route-eth0 and add following lines :

192.168.60.0/24 via 192.168.50.1

# The above config sets static routing for network 191.168.60.0/24 via 192.168.50.1 router.
