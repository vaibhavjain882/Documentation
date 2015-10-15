```
#!/bin/bash

TotalUsedMemory=$(free -m |gawk '{print $3}' |head -2 |tail -1)
TUM=$TotalUsedMemory

IdleCpu=$(mpstat |gawk '{print $12}' |tail -1)

LoadAverage=$(uptime |cut -c55-)

DiskUsages=$(df -h)

neo4j=$(ps aux |grep neo4j |gawk '{print $2}' |head -1)
if [ $neo4j -ge 1 ]
then
nmu=$(ps -p $neo4j -O rss | gawk '{ count ++; sum += $2 }; END {count --; print sum/1024};' | awk -F "." '{print $1}')
ncpu=$(ps -p $neo4j -o pcpu | sed -n '2p' |awk -F "." '{print $1}')
else
echo -e "Neo4j Service is not running" | mail -s "Tomcat Service is not running" -r neopoc@neo4j saurabh.kumar@travenues.com
fi

if [ $nmu -ge 1000 ] || [ $ncpu -ge 50 ]
then
echo -e "Total Used Memory:  $TUM MB \n\nIdle CPU in Percentage: $IdleCpu% \n\nLoad Average:  $LoadAverage \n\nCpu Usage by neo4j service: $ncpu \n\n Total memory used by neo4j: $nmu MB \n\nAll Disk Usage:  $DiskUsages \n\n" | mail -s "Neo4j Server Status is Critical." -r ldap1@tomcat saurabh.kumar@travenues.com
echo "0" > /tmp/Neo4jStatus

echo "0" > /tmp/Neo4jFlag
else
echo "1" > /tmp/Neo4jFlag
fi
S=$(cat /tmp/Neo4jStatus)

F=$(cat /tmp/Neo4jFlag)

if [ "$F" -eq 1 ] && [ "$S" -eq 0 ]; then
echo -e "Total Used Memory:  $TUM MB \n\nIdle CPU in Percentage: $IdleCpu% \n\nLoad Average:  $LoadAverage \n\nCpu Usage by neo4j service: $ncpu \n\n Total memory used by neo4j: $nmu MB \n\nAll Disk Usage:  $DiskUsages \n\n" | mail -s "Neo4j Server Status is Okay Now." -r ldap1@tomcat saurabh.kumar@travenues.com

rm -f /tmp/Neo4jStatus
fi

```
