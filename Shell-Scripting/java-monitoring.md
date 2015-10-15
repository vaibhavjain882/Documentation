```
#!/bin/bash

TotalUsedMemory=$(free -m |gawk '{print $3}' |head -2 |tail -1)
TUM=$TotalUsedMemory

IdleCpu=$(mpstat |gawk '{print $12}' |tail -1)

LoadAverage=$(uptime |cut -c55-)

DiskUsages=$(df -h)
tom-1=$(ps aux |grep tomcat1 |gawk '{print $2}' |head -1)
tom-2=$(ps aux |grep tomcat2 |gawk '{print $2}' |head -1)
if [ "tom-1" -lt 1 ] && [ "tom-2" -lt 1 ]; then
echo -e "Tomcat Service is not Running" | mail -s "Tomcat Service Is not Running" -r ldap1@tomcat saurabh.kumar@travenues.com
fi

for i in 1 2
do
tomcat=$(ps aux |grep tomcat$i |gawk '{print $2}' |head -1)
if [ $tomcat -ge 1 ]
then
tmu=$(ps -p $tomcat -O rss | gawk '{ count ++; sum += $2 }; END {count --; print sum/1024};' | awk -F "." '{print $1}')
ps -p $tomcat -o pcpu | sed -n '2p' > /tmp/cpu-$i.txt
tcpu=$(ps -p $tomcat -o pcpu | sed -n '2p' |awk -F "." '{print $1}')

if [ $tmu -ge 4000 ] || [ $tcpu -ge 50 ]
then
ps -p $tomcat -L --sort=-pcpu,+pmem -o pid,tid,uname,pcpu,pmem,comm |head -10 | gawk '{print $2}' |tail -9> /tmp/pids.txt
(echo "obase=16" ; cat /tmp/pids.txt ) | bc > /tmp/tid.txt
sed -i -e 's/^/0x/' /tmp/tid.txt
tac /tmp/tid.txt > /tmp/tids.txt

runuser deploy -c "jstack $tomcat" > thread-dump-$i.txt
cat thread-dump-$i.txt |grep -A500 -f tids.txt | grep -m10 "^$" -B 500 > /tmp/val-$i.txt
echo -e "Total Used Memory:  $TUM MB \n\nIdle CPU in Percentage: $IdleCpu% \n\nLoad Average:  $LoadAverage \n\nCpu Usage by Tomcat-$i: $(cat /tmp/cpu-$i.txt)% \n\nAll Disk Usage:  $DiskUsages \n\nStatistics for Tomcat$i:\n\n $(cat val-$i.txt) " | mail -a "/tmp/thread-dump-$i.txt" -s "tomcat Server Status is Critical." -r ldap1@tomcat saurabh.kumar@travenues.com
echo "0" > /tmp/JavaStatus

echo "0" > /tmp/JavaFlag
else
echo "1" > /tmp/JavaFlag
fi
fi
done

S=$(cat /tmp/JavaStatus)

F=$(cat /tmp/JavaFlag)

if [ "$F" -eq 1 ] && [ "$S" -eq 0 ]; then
echo -e "Now Tomcat Server is Okay" | mail -s "Now Tomcat$i Server is Okay" -r ldap1@tomcat saurabh.kumar@travenues.com
rm -f /tmp/JavaStatus
fi

```
