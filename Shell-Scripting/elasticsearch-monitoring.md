```
#!/bin/bash
response=$(curl -s 10.31.33.16:9200/_cat/health |gawk '{print $4}')
pid=$(ps aux |grep elasticsearch |gawk '{print $2}' |head -1)
ElasticPM=$(ps -p $pid -O rss | gawk '{ count ++; sum += $2 }; END {count --; print sum/1024};' | awk -F "." '{print $1}')
ElasticCU=$(ps -p $pid -L --sort=-pcpu,+pmem -o pid,tid,uname,pcpu,pmem,comm |head -10 |gawk '{ print $4 }' |head -2 |tail -1 |awk -F "." '{ print $1 }')
TotalUsedMemory=$(free -m |gawk '{print $3}' |head -2 |tail -1)
CpuUsage=$(mpstat |gawk '{print $12}' |tail -1)
LoadAverage=$(uptime |cut -c54-)
DiskUsage=$(df -h)
TotalUsedMemory=$(free -m |gawk '{print $3}' |head -2 |tail -1)
CpuUsage=$(mpstat |gawk '{print $12}' |tail -1)
if [ "$response" == "green" ];then
flag=1
if [ "$ElasticCU" -ge 40 ] && [ "$ElasticPM" -ge 20000 ]; then
echo -e "10.31.33.16 - Elasticssearch Server UP \n\n Memory Used By Elasticsearch: $ElasticPM MB\n\n CPU used by ElasticSearch: $ElasticCU \n\n TotalUsedMemory: $TotalUsedMemory MB\n\n Idle CPU: $CpuUsage \n\n Load Average: $LoadAverage \n\n Disk Usage: $DiskUsage " |mail -s "10.31.33.16 - Elasticssearch Server UP" -r ldap1@elasticsearch saurabh.kumar@travenues.com
fi

elif [ "$response" == "red" ] || [ "$ElasticCU" -ge 40 ] || [ "$ElasticPM" -ge 20000 ]; then
status=0
echo "$status" > /tmp/esstatus
echo -e "10.31.33.16 - Elasticssearch Server Down\n\n Memory Used By Elasticsearch: $ElasticPM MB\n\n CPU used by ElasticSearch: $ElasticCU \n\n TotalUsedMemory: $TotalUsedMemory MB\n\n CPU Usage: $CpuUsage \n\n Load Average: $LoadAverage \n\n Disk Usage: $DiskUsage" |mail -s "10.31.33.16 - Elasticssearch Server Down" -r ldap1@elasticsearch saurabh.kumar@travenues.com

elif [ "$response" == "yellow" ] || [ "$ElasticCU" -ge 40 ] || [ "$ElasticPM" -ge 20000 ]; then
status=0
echo "$status" > /tmp/esstatus
echo -e "10.31.33.16 - shards are allocating \n\n Memory Used By Elasticsearch: $ElasticPM MB\n\n CPU used by ElasticSearch: $ElasticCU \n\n TotalUsedMemory: $TotalUsedMemory MB\n\n CPU Usage: $CpuUsage \n\n Load Average: $LoadAverage \n\n Disk Usage: $DiskUsage " |mail -s "10.31.33.16 - shards are allocating" -r ldap1@elasticsearch saurabh.kumar@travenues.com

elif [ ! $response ] || [ "$ElasticCU" -ge 40 ] || [ "$ElasticPM" -ge 20000 ]; then
status=0
echo "$status" > /tmp/esstatus
echo -e "10.31.33.16 - Elasticssearch process is not running \n\n Memory Used By Elasticsearch: $ElasticPM MB\n\n CPU used by ElasticSearch: $ElasticCU \n\n TotalUsedMemory: $TotalUsedMemory MB\n\n Idle CPU: $CpuUsage \n\n Load Average: $LoadAverage \n\n Disk Usage: $DiskUsage " |mail -s "10.31.33.16 - Elasticssearch process is not running" -r ldap1@elasticsearch saurabh.kumar@travenues.com
fi

S=$(cat /tmp/esstatus)
echo "$S"

if [ "$S" -eq 0 ] && [ "$flag" -eq 1 ]; then
echo -e "10.31.33.16 - Elasticssearch Server UP \n\n Memory Used By Elasticsearch: $ElasticPM MB\n\n CPU used by ElasticSearch: $ElasticCU \n\n TotalUsedMemory: $TotalUsedMemory MB\n\n Idle CPU: $CpuUsage \n\n Load Average: $LoadAverage \n\n Disk Usage: $DiskUsage " |mail -s "10.31.33.16 - Elasticssearch Server UP" -r ldap1@elasticsearch saurabh.kumar@travenues.com
rm -f /tmp/esstatus
fi
```