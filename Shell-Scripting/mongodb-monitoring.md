```
#!/bin/bash
mongo --eval "db.stats()"

RESULT=$?

if [ $RESULT -ne 0 ]; then
mail -s "Mongo is not running" -r ldap1@mongo  saurabh.kumar@travenues.com
else
    # echo "mongodb running!"
MongoPM=$(ps -C mongod -O rss | gawk '{ count ++; sum += $2 }; END {count --; print sum/1024};' | awk -F "." '{print $1}')
MongoCU=$(ps -C mongod -L --sort=-pcpu,+pmem -o pid,tid,uname,pcpu,pmem,comm |head -10 |gawk '{ print $4 }' |head -2 |tail -1 |awk -F "." '{ print $1 }')
TotalUsedMemory=$(free -m |gawk '{print $3}' |head -2 |tail -1)
CpuUsage=$(mpstat |gawk '{print $12}' |tail -1)
LoadAverage=$(uptime |cut -c54-)
DiskUsage=$(df -h)
MS=$(mongostat -n 10)
MT=$(timeout 5 /usr/bin/mongotop -n 11)
mongo --eval "printjson(db.serverStatus())" > /tmp/serverstatus
if [ "$MongoPM" -ge 1000 ] || [ "$MongoCU" -ge 60 ]; then
echo -e "Total Used Memory: $TotalUsedMemory MB \n\nIdle CPU in Percentage: $CpuUsage% \n\nLoad Average: $LoadAverage \n\nCpu Usage by Mongo: $MongoCU \n\n Memory Usage By Mongo: $MongoPM \n\nAll Disk Usage: $DiskUsage \n\nMongostat Log:\n\n $MS \n\nMongoTop Log: \n\n $MT" | mail -a "/tmp/serverstatus" -s "Mongo Server Status is Critical." -r ldap1@tomcat saurabh.kumar@travenues.com
fi
fi
```