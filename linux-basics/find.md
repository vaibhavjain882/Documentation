Find Files Using Name in Current Directory
find . -name tecmint.txt

Find Files Using Name and Ignoring Case:
find /home -iname tecmint.txt

Find Directories Using Name:
find / -type d -name Tecmint

Find PHP Files Using Name:
find . -type f -name tecmint.php

Find Files With 777 Permissions:
find . -type f -perm 0777 -print

Find Files Without 777 Permissions:
find / -type f ! -perm 777

Find Read Only Files:
find / -perm /u=r

Find Executable Files:
find / -perm /a=x

Find Files with 777 Permissions and Chmod to 644:
find / -type f -perm 0777 -print -exec chmod 644 {} \;

Find and remove single File:
find . -type f -name "tecmint.txt" -exec rm -f {} \;

Find all Empty Files:
find /tmp -type f -empty

Find Single File Based on User:
find / -user root -name tecmint.txt

Find all Files Based on User:
find /home -user tecmint

Find Particular Files of User:
find /home -user tecmint -iname "*.txt"

Find Last 50 Days Modified Files:
find / -mtime 50

Find Last 50 Days Accessed Files:
find / -atime 50

Find Last 50-100 Days Modified Files:
find / -mtime +50 –mtime -100

Find Changed Files in Last 1 Hour:
find / -cmin -60

Find 50MB Files:
find / -size 50M

Find Specific Files and Delete:
find / -type f -name *.mp3 -size +10M -exec rm {} \;

