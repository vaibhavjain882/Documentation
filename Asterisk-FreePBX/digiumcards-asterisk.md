First get a PRI Line installed in your office.

1. First you need to check whether card is in E1 or T1 mode. Take card outside and check for the E1/T1 changeover jumper in the card, if your PRI line is E1 then you need close the jumper , if T1 then it should be open. then insert the card into the server

2. Make sure card is detected by server , type the following command in Linux shell
```
lspci
```
you will get following output:
```
0e:08.0 Ethernet controller: Digium, Inc. Wildcard TE121 single-span T1/E1/J1 card (PCI-Express) (rev 11) 
```

3. Once the card is detected type the below commands to autoconfigure the card.
```
dahdi_genconf -v

#This commans shows the below output:

Default parameters from /etc/dahdi/genconf_parameters
Generating /etc/dahdi/system.conf 
Generating /etc/asterisk/dahdi-channels.conf
```
4. you need to enable the dahdi-channels.conf in asterisk conf files. open the file  /etc/asterisk/chan_dahdi.conf  using vi editor as shown below:
```
vi /etc/asterisk/chan_dahdi.conf
```
go to last line  and press o  and then add the below line:
```
#include dahdi-channels.conf
```

5. Dahdi driver reloading:
```
asterisk -vvvvvvr
module unload chan_dahdi.so
module load chan_dahdi.so
```

6. config confirmation
```
type  dahdi_cfg -vv  in linux console
```

7. card and line status;
```
dahdi show status
dahdi show channels
pri show spans
```

8. Dialplan to dial via PRI:
```
If your using Freepbx/ elastix/ piaf/ asterisknow

Create a Dahdi trunk,  with channel name as  g0, and then create outbound routes.
```
#### Instead Of installing packages manually, you can install trixbox:
Download the latest Trixbox CE stable release from Trixbox Website
Burn it in to CD
Identify a suitable hardware ( or VMware virtual server)
Boot the machine with the CD ( For vmware virtual server you can directly mount the ISO)
And follow the below simple steps

