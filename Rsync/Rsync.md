### Rsync: 

Rsync, which stands for "remote sync", is a remote and local file synchronization tool. It uses an algorithm that minimizes the amount of data copied by only moving the portions of files that have changed. If any file is at destination but not at source then rsync deletes that file.

#### Rsync Demo:

I am going to show you a practical demo. If you have some codes on your preproduction server and wants to push that code at multiple production servers then follow below process:

```
yum install -y rsyncd
```

Suppose your code directory is /tmp/project-1, username and groupname for this directory is testuser, preproduction server name is "preprod" and live servers is prod-1 and prod-2. For better practise your prod servers should be the exact replica of preprod server.

Now, Make an configuration file in /etc/ directory with following configuration:

vi /etc/rsyncd.conf

```
motd file = /etc/rsyncd.motd
log file = /var/log/rsyncd.log
pid file = /var/run/rsyncd.pid
lock file = /var/run/rsync.lock
uid = nobody
gid = nobody
use chroot = yes
read only = no

[check]         # this is your modulename
   path = /tmp/project-1
   comment = Test Rsync
   uid = testuser
   gid = testuser   
```
vi /etc/rsyncd.motd

```
welcome to preprod
```

Make an init script for rsyncd service in /etc/init.d directory and give 755 permision to it. init script is given below:

```
#! /bin/sh
#
# chkconfig:   2345 50 50
# description: The rsync daemon

# source function library
 . /etc/rc.d/init.d/functions

PROG='/usr/bin/rsync'
BASE=${0##*/}

# Adapt the --config parameter to point to your rsync daemon configuration
# The config file must contain following line:
#  pid file = /var/run/<filename>.pid
# Where <filename> is the filename of the init script (= this file)
OPTIONS="--daemon --config=/etc/rsyncd.conf"

case "$1" in
  start)
    echo -n $"Starting $BASE: "
    daemon --check $BASE $PROG $OPTIONS
    RETVAL=$?
    [ $RETVAL -eq 0 ] && touch /var/lock/subsys/$BASE
    echo
    ;;
  stop)
    echo -n $"Shutting down $BASE: "
    killproc $BASE
    RETVAL=$?
    [ $RETVAL -eq 0 ] && rm -f /var/lock/subsys/$BASE
    echo
    ;;
  restart|force-reload)
    $0 stop
    sleep 1
    $0 start
    ;;
  *)
    echo "Usage: $0 {start|stop|restart|force-reload}" >&2
    exit 1
    ;;
esac

exit 0
```
Now:

```
 $ chmod 755 rsyncd
 $ service rsyncd start
```
Now, same setup should be on all your live servers except content of rsyncd.motd here your servername will be different.

#### Run Rsync with following parameters:

```
rsync --times --delete --stats --recursive -p -o /tmp/project-1 192.168.1.233::check
```

Now all done. Cheers :)


