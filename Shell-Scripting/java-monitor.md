```
#!/bin/bash

TotalUsedMemory=$(free -m |gawk '{print $3}' |head -2 |tail -1)
TUM=$TotalUsedMemory

IdleCpu=$(mpstat |gawk '{print $12}' |tail -1)

LoadAverage=$(uptime |cut -c55-)

DiskUsages=$(df -h)
for i in 1 2
do
tomcat=$(ps aux |grep tomcat$i |gawk '{print $2}' |head -1)
if [ $tomcat -ge 1 ]
then
tmu=$(ps -p $tomcat -O rss | gawk '{ count ++; sum += $2 }; END {count --; print sum};')
ps -p $tomcat -o pcpu | sed -n '2p' > /tmp/cpu-$i.txt
tcpu=$(ps -p $tomcat -o pcpu | sed -n '2p' |awk -F "." '{print $1}')

if [ $tmu -ge 1000000 ] || [ $tcpu > 50 ]
then
ps -p $tomcat -L --sort=-pcpu,+pmem -o pid,tid,uname,pcpu,pmem,comm |head -10 | gawk '{print $2}' |tail -9> /tmp/pids.txt
(echo "obase=16" ; cat /tmp/pids.txt ) | bc > /tmp/tid.txt
sed -i -e 's/^/0x/' /tmp/tid.txt
tac /tmp/tid.txt > /tmp/tids.txt

runuser deploy -c "jstack $tomcat" > thread-dump-$i.txt
cat thread-dump-$i.txt |grep -A500 -f tids.txt | grep -m10 "^$" -B 500 > /tmp/val-$i.txt
fi
fi
done
echo -e "Total Used Memory:  $TUM MB \n\nIdle CPU in Percentage: $IdleCpu% \n\nLoad Average:  $LoadAverage \n\nCpu Usage by Tomcat-1: $(cat /tmp/cpu-1.txt)%  \n\nCpu Usage by Tomcat-2: $(cat /tmp/cpu-2.txt)% \n\nAll Disk Usage:  $DiskUsages \n\nStatistics for Tomcat1:\n\n $(cat val-1.txt) \n\nStatistics for Tomcat2:\n\n $(cat val-2.txt) " | mail -a "/tmp/thread-dump-1.txt"  -a "/tmp/thread-dump-2.txt" -s "tomcat Server Status is Critical." -r ldap1@tomcat saurabh.kumar@travenues.com
```