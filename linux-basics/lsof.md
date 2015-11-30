### lsof command examples:
lsof: list of open files
Columns in lsof command:

COMMAND  PID       USER   FD      TYPE     DEVICE  SIZE/OFF       NODE NAME

Description: FD- file descriptor
FD – Represents the file descriptor. Some of the values of FDs are,

cwd – Current Working Directory
txt – Text file
mem – Memory mapped file
mmap – Memory mapped device
NUMBER – Represent the actual file descriptor. The character after the number i.e ‘1u’, represents the mode in which the file is opened. r for read, w for write, u for read and write.

TYPE – Specifies the type of the file. Some of the values of TYPEs are,

REG – Regular File
DIR – Directory
FIFO – First In First Out
CHR – Character special file

#### 1. List processes which opened a specific file

```
# lsof /var/log/syslog
```

#### 2. List opened files under a directory
```
# lsof +D /var/log/
```

#### 3. List opened files based on process names starting with
```
# lsof -c ssh -c init
```

#### 4. List processes using a mount point

```
# lsof /home
```

#### 5. List files opened by a specific user
```
# lsof -u lakshmanan
```

#### 6. Sometimes you may want to list files opened by all users, expect some 1 or 2:
```
# lsof -u ^lakshmanan
```

#### 7. List all open files by a specific process
```
lsof -p 1753
```

#### 8. Kill all process that belongs to a particular user
```
# kill -9 `lsof -t -u lakshmanan`
```

#### 9. Similarly you can also use ‘-t’ in many ways. For example, to list process id of a process which opened /var/log/syslog can be done by:
```
# lsof -t /var/log/syslog
```

#### 10. Combine more list options using OR/AND
```
# lsof -u lakshmanan -c init -a
```
#### 11. Execute lsof in repeat mode

lsof also support Repeat mode. It will first list files based on the given parameters, and delay for specified seconds and again list files based on the given parameters. It can be interrupted by a signal

Repeat mode can be enabled by using ‘-r’ or ‘+r’. If ‘+r’ is used then, the repeat mode will end when no open files are found. ‘-r’ will continue to list,delay,list until a interrupt is given irrespective of files are opened or not.

```
# lsof -u lakshmanan -c init -a -r5
```
###Finding Network Connection

#### 1. List all network connection:

```
# lsof -i
```

#### 2. List all network files in use by a specific process
```
# lsof -i -a -p 234
```
#### 3. List processes which are listening on a particular port:
```
# lsof -i :25
```

#### 4. List all TCP or UDP connections:
```
# lsof -i tcp; lsof -i udp;
```

#### 5. List all Network File System ( NFS ) files
```
# lsof -N -u lakshmanan -a
```
