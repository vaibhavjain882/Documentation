1. Log on to the domain controller.
2. Enter the following at the command line:
```
W32tm /config /manualpeerlist:<timeserver> /syncfromflags:manual
```
timeserver is a space-delimited list of DNS and/or IP addresses. When specifying multiple time servers, enclose the list in quotation marks.

3.Update the Windows Time Service configuration. At the command line, you can either enter W32tm /config /update, or you can enter the following:
```
Net stop w32time
Net start w32time
```
If a system isn't a member of a domain, you must manually configure it to synchronize with a specified time source. Follow these steps:
1. Go to Start | Control Panel, and double-click Date And Time.
2. On the Internet Time tab, select a time server from the drop-down list, or enter the DNS name of your network's internal time source.
3. Click Update Now, click Apply, and click OK.

It's important to make sure that any access control lists on your network allow UDP port 123 to and from systems to the selected time source.

w32tm /query /status: for seeing status
For linux ntp client install ntpdate tool and fire a command 
ntpdate dnsserevrip
