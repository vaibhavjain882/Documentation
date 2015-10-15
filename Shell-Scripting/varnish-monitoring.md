```
#!/bin/bash

NC=$(netstat -anp | grep 81 | grep -c EST)

Uptime=$(varnishstat -1 | grep uptime |awk '{print $2}')

BE=$(varnishstat -1 | grep "backend_fail")

AVT=$(varnishadm debug.health |grep probes | cut -b 37-45 |head -n 2 |tail -n 1)

CP=$(varnishstat -1 |grep child_panic)

CD=$(varnishstat -1 |grep child_dump)

CS=$(varnishstat -1 |grep child_start)

varnishstat -1 > /tmp/VarnishStat

VarnishPM=$(ps -C varnishd1 -O rss | gawk '{ count ++; sum += $2 }; END {count --; print sum/1024};' | awk -F "." '{print $1}')

VarnishCU=$(ps -C varnishd1 -L --sort=-pcpu,+pmem -o pid,tid,uname,pcpu,pmem,comm |head -10 |gawk '{ print $4 }' |head -2 |tail -1 |awk -F "." '{ print $1 }')

TotalUsedMemory=$(free -m |gawk '{print $3}' |head -2 |tail -1)

CpuUsage=$(mpstat |gawk '{print $12}' |tail -1)

LoadAverage=$(uptime |cut -c54-)

DiskUsage=$(df -h)

if [ "$VarnishPM" -ge 1000 ] || [ "$VarnishCU" -ge 60 ]; then

echo -e "Total Used Memory: $TotalUsedMemory MB \n\nIdle CPU in Percentage: $CpuUsage% \n\nLoad Average: $LoadAverage \n\nCpu Usage by Varnish: $VarnishCU \n\n Memory Usage By Varnish: $VarnishPM \n\nAll Disk Usage: \n\n$DiskUsage" | mail -a "/tmp/VarnishStat" -s "Mongo Server Status is Critical." -r ldap1@varnish saurabh.kumar@ixigo.com

echo "0" > /tmp/VarnishStatus

echo "0" > /tmp/VarnishFlag

else

echo "1" > /tmp/VarnishFlag

fi

S=$(cat /tmp/VarnishStatus)

F=$(cat /tmp/VarnishFlag)

if [ "$F" -eq 1 ] && [ "$S" -eq 0 ]; then

echo -e "Total Used Memory: $TotalUsedMemory MB \n\nIdle CPU in Percentage: $CpuUsage% \n\nLoad Average: $LoadAverage \n\nCpu Usage by Varnish: $VarnishCU \n\n Memory Usage By Varnish: $VarnishPM \n\nAll Disk Usage: $DiskUsage" | mail -a "/tmp/VarnishStat" -s "Mongo Server Status is OK Now." -r ldap1@varnish saurabh.kumar@ixigo.com

rm -f /tmp/VarnishStatus 

fi
```
