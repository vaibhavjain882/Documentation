### Redis:

Redis is a key-value in memory data store typically used for caches and other such mechanisms to speed up web applications. 
Like memcached, everything is held in memory. Redis does persist to disk, but it doesn’t synchronously store data to disk as you write it.
The main reason why we do not use Redis as primary database is :

1. You have to be able to fit all your data in memory, and
2. If your server fails between disk syncs you lose anything that was sitting in memory.

#### Redis Keys:

Redis keys are binary safe, this means that you can use any binary sequence as a key, from a string like "foo" to the content of a JPEG file. The empty string is also a valid key.
A few other rules about keys:
Very long keys are not a good idea, for instance a key of 1024 bytes is a bad idea not only memory-wise, but also because the lookup of the key in the dataset may require several costly key-comparisons. Even when the task at hand is to match the existence of a large value, to resort to hashing it (for example with SHA1) is a better idea, especially from the point of view of memory and bandwidth.
Very short keys are often not a good idea. There is little point in writing "u1000flw" as a key if you can instead write "user:1000:followers". The latter is more readable and the added space is minor compared to the space used by the key object itself and the value object. While short keys will obviously consume a bit less memory, your job is to find the right balance.
Try to stick with a schema. For instance "object-type:id" is a good idea, as in "user:1000". Dots or dashes are often used for multi-word fields, as in "comment:1234:reply.to" or "comment:1234:reply-to".
The maximum allowed key size is 512 MB.
```
###Queries:

#### CLI Queries:

####Tracing:-
monitor: Watch current live commands. Use this with care on production. Cancel with Ctrl-C.

####Slow Queries:-

slowlog get 25		# print top 25 slow queries
slowlog len		
slowlog reset

####Search Keys:-

keys pattern		# Find key matching exactly
keys pattern*		# Find keys matching in back
keys *pattern*		# Find keys matching somewhere
keys pattern*		# Find keys matching in front

####Generic:

del <key>
dump <key>		# Serialize key
exists <key>
expire <key> <seconds>
get <key>	
set <key> <value>
setnx <key> <value>	# Set key value only if key does not exist

####Scalars:- Batch commands:

mget <key> <key> ...
mset <key> <value> <key> <value> ...

####Scalars:- Counter commands:

incr <key>
decr <key>

####Lists:

lrange <key> <start> <stop>
lrange mylist 0 -1		# Get all of a list
lindex mylist 5			# Get by index
llen mylist			# Get length

lpush mylist "value"
lpush mylist 5			
rpush mylist "value"

lpushx mylist 6			# Only push in mylist exists
rpushx mylist 0 

lpop mylist
rpop mylist

lrem mylist 1 "value"		# Remove 'value' count times
lset mylist 2 6			# mylist[2] = 6
ltrim <key> <start> <stop>

#### Hashes: Batch Commands:

hmget <key> <key> ...
hmset <key> <value> <key> <value> ...

#### Hashes: Counter Commands:

hincrby myhash field1 1
hincrby myhash field1 5
hincrby myhash field1 -1

hincrbrfloat myhash field2 1.123445 

#### Sets: FIXME
#### Sorted Sets: FIXME

####CLI Scripting:
For scripting just pass commands to "redis-cli". For example:
redis-cli INFO | grep connected

#### Changing Runtime Configuration:
gives you a list of all active configuration variables you can change.

CONFIG GET *

Note that keys and values are alternating and you can change each key by issuing a "CONFIG SET" command like:
CONFIG SET timeout 900

####Databases: Multiple databases:
Redis has a concept of separated namespaces called "databases". You can select the database number you want to use with "SELECT". By default the database with index 0 is used. So issuing

redis 127.0.0.1:6379> SELECT 1

To find out how many databases there are you might want to run redis-cli from the shell:

$ redis-cli INFO | grep ^db

####Dropping Databases:
To drop the currently selected database run

FLUSHDB

to drop all databases at once run:

FLUSHALL

### Replication:

#### Checking for replication:
To see if the instance is a replication slave or master issue:

redis 127.0.0.1:6379> INFO

####Setting up Replication:
If you quickly need to set up replication just issue:

SLAVEOF <IP> <port>

on a machine that you want to become slave of the given IP. It will immediately get values from the master. Note that this instance will still be writable. If you want it to be read-only change the redis config file (only available in most recent version, e.g. not on Debian). To revert the slave setting run.

SLAVEOF NO ONE

#### Performance Testing:

Install the Redis tools and run the provided benchmarking tool:

redis-benchmark -h <host> [-p <port>]

####Debugging Latency:

First measure system latency on your Redis server with

redis-cli --intrinsic-latency 100

and then sample from your Redis clients with

redis-cli --latency -h <host> -p <port>

If you have problems with high latency check if transparent huge pages are disabled. Disable it with

echo never > /sys/kernel/mm/transparent_hugepage/enabled

####Dump Database Backup:
As Redis allows RDB database dumps in background, you can issue a dump at any time. Just run:

BGSAVE

When running this command Redis will fork and the new process will dump into the "dbfilename" configured in the Redis configuration without the original process being blocked. Of course the fork itself might cause an interruption. Use "LASTSAVE" to check when the dump file was last updated. For a simple backup solution just backup the dump file. If you need a synchronous save run "SAVE" instead of "BGSAVE".

#### Listing connections:

CLIENT LIST

and you can terminate connections with:

CLIENT KILL <IP>:<port>

####Monitoring Traffic:

additionally use "SLOWLOG" to track the slowest queries in an interval. For example

SLOWLOG RESET
```
