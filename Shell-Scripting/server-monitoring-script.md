```
#!/bin/bash
JavaP=$(ps -C java -O rss | gawk '{ count ++; sum += $2 }; END {count --; print count;}')
#echo "$JavaP"
JavaPM=$(ps -C java -O rss | gawk '{ count ++; sum += $2 }; END {count --; print sum/1024};')
#echo "$JavaPM"
TotalUsedMemory=$(free -m |gawk '{print $3}' |head -2 |tail -1)
TUM=$TotalUsedMemory
#echo "$TotalUsedMemory"
CpuUsage=$(mpstat |gawk '{print $4}' |tail -1)
#echo "$CpuUsage"
LoadAverage=$(uptime |cut -c54-)
#echo "$LoadAverage"
LoadAverage15=$(uptime |cut -c54- |cut -c13-)
#declare -i LA
LA=$LoadAverage15
#echo "$LoadAverage15"
DiskUsage=$(df -h)
#echo "$DiskUsage"
if [ "$TUM" -ge 7500 ] || [ '\( `$LA >= 4` \)' ]
then
echo "Number of Java processes:$JavaP \nMemory used by Java Processes:$JavaPM \nTotal Used Memory:$TotalUsedMemory \n Cpu Usage in Percentage:$CpuUsage \nLoad Average:$LoadAverage\n All Disk Usage:$DiskUsage" | mail -s "NeoPOC Server Status is Critical." -r neopoc@neopoc saurabh.kumar@travenues.com
else
echo "Status Okay" | mail -s "NeoPOC Server Status is OK." -r neopoc@neopoc saurabh.kumar@travenues.com
fi

```
