#### memcached: 
Memcached is an open source, high-performance, distributed memory caching system intended to speed up dynamic web applications by reducing the database load. It is a key-value dictionary of strings, objects, etc, stored in the memory, resulting from database calls, API calls, or page rendering.

Default port is 11211.
####memcached commands:
````
telnet host port
```
<key> : the key of the data stored to retrieve
<key>* mean you can request value of multiple keys separated by whitespace

These commands return :
VALUE <flag> <bytes>\r\n<data> (see the following for explanation of <bytes> and <flag>)
END indicate the end of response

####set : 
```
set <key> <flags> <exptime> <bytes> [noreply]  Ex: set key 0 900 4 data
```
####get : 
```
get key
```

####Gets : 
```
gets <key>*\r\n , get value with cas token which is "Store data only if no one else has updated since the last fetch, determined with the cas tokem from gets command"
```
####add : 
```
Store key/value pair in Memcached, but only if the server doesn’t already hold data for this key. add <key> <flags> <exptime> <bytes> [noreply]\r\n<value>\r\n
```
####replace :
Store key/value pair in Memcached, but only if the server already hold data for this key
```
replace <key> <flags> <exptime> <bytes> [noreply]\r\n<value>\r\n
```

####append :
Add value to an existing key after existing data
Append does not take <flags> or <exptime> parameters but you must provide them 
```
	
	append key 0 900 2 10
```
####prepend :
Add value to an existing key before existing data
Prepend does not take <flags> or <exptime> parameters but you must provide them
```
 prepend <key> <flags> <exptime> <bytes> [noreply]\r\n<value>\r\n
 ```
####cas :
Store data only if no one else has updated since the last fetch, determined with the cas tokem from gets command
```
cas <key> <flags> <exptime> <bytes> <cas unique> [noreply]\r\n
cas key 0 900 2 &lt;cas unique&gt; 10
```

####delete 
```
Parameters :
<key> : the key to delete
[<time>] : optional time in second before real deletion of item
[noreply] : optional parameter that inform the server to not send the reply

These command can return :
DELETED to indicate success
NOT_FOUND indicate that the item did not exist or has been deleted

delete key

####incr
Increment value associated with key in Memcached, item must exist, increment command will not create it
The limit of increment is the 64 bit mark
```
	
	incr key 12
	```
####decr : same as increment
####touch
The “touch” command is used to update the expiration time of an existing item without fetching it.
```
touch key 1800
```
#### stats
Return general-purpose statistics like uptime, version, memory occupation, …
Return items statistics, will display items statistics (count, age, eviction, …) organized by slabs ID
Return slabs statistics, will display slabs statistics (size, memory usage, commands count, …) organized by slabs ID
```
stats
stats items
stats slabs
```
####flush_all
Flush the server key/value pair (invalidating them) after a optional [<time>] period
It always return OK
```
flush_all [<time>] [noreply]\r\n
flush_all 10
```
####version
```
Return the Memcached server version
```
####verbosity
Change the verbosity ouptut of Memcached server
It always return OK

Telnet command : verbosity <level> [noreply]\r\n

####quit
Server close connection when receiving this command


Parameters :
<key> : the key of the data stored
<flags> : 32-bit unsigned integer that the server store with the data (provided by the user), and return along the data when the item is retrieved
<exptime> : expiration time in seconds, 0 mean no delay, if exptime is superior to 30 day, Memcached will use it as a UNIX timestamps for expiration
<bytes> : number of bytes in the data block
<cas unique> : unique 64-bit value of an existing entry (retrieved with gets command) to use with cas command
[noreply] : optional parameter that inform the server to not send the reply

These commands can return :
STORED to indicate success
NOT_STORED indicate that the data was not stored because condition for “add” or “replace” command wasn’t met, or the item is in a delete queue
EXISTS indicate that the item you are trying to store with “cas” command has been modified since last fetch
NOT_FOUND indicate that the item did not exist or has been deleted


#### memcahed configuration options:
```
# Run memcached as a daemon. This command is implied, and is not needed for the
# daemon to run. See the README.Debian that comes with this package for more
# information.
-d

# Log memcached's output to /var/log/memcached
logfile /var/log/memcached.log

# Be verbose
# -v

# Be even more verbose (print client commands as well)
# -vv

# Start with a cap of 64 megs of memory. It's reasonable, and the daemon default
# Note that the daemon will grow to this size, but does not start out holding this much
# memory
-m 64

# Default connection port is 11211
-p 11211
# Run the daemon as root. The start-memcached will default to running as root if no
# -u command is present in this config file
-u memcache

# Specify which IP address to listen on. The default is to listen on all IP addresses
# This parameter is one of the only security measures that memcached has, so make sure
# it's listening on a firewalled interface.
-l 127.0.0.1

# Limit the number of simultaneous incoming connections. The daemon default is 1024
# -c 1024

# Lock down all paged memory. Consult with the README and homepage before you do this
# -k

# Return error when memory is exhausted (rather than removing items)
-M

# Maximize core file limit
# -r
```


