###Varnish
Varnish is an HTTP accelerator designed for content-heavy dynamic web sites as well as heavily consumed APIs. 
Varnish stores data in virtual memory and leaves the task of deciding what is stored in memory and what gets paged out to disk to the operating system. This helps avoid the situation where the operating system starts caching data while it is moved to disk by the application.

Varnish is heavily threaded, with each client connection being handled by a separate worker thread. When the configured limit on the number of active worker threads is reached, incoming connections are placed in an overflow queue; when this queue reaches its configured limit incoming connections will be rejected.

First configure varnish configuration file /etc/sysconfig/varnish after installing it :
```

NFILES=131072

MEMLOCK=82000

NPROCS="unlimited"

RELOAD_VCL=1

VARNISH_VCL_CONF=/etc/varnish/default.vcl
VARNISH_LISTEN_PORT=80
VARNISH_ADMIN_LISTEN_ADDRESS=127.0.0.1
VARNISH_ADMIN_LISTEN_PORT=8080
VARNISH_SECRET_FILE=/etc/varnish/secret
VARNISH_MIN_THREADS=50
VARNISH_MAX_THREADS=1000
VARNISH_THREAD_TIMEOUT=120
VARNISH_STORAGE_SIZE=256M
VARNISH_STORAGE="malloc,${VARNISH_STORAGE_SIZE}"
VARNISH_TTL=120
DAEMON_OPTS="-a ${VARNISH_LISTEN_ADDRESS}:${VARNISH_LISTEN_PORT} \
-f ${VARNISH_VCL_CONF} \
-T ${VARNISH_ADMIN_LISTEN_ADDRESS}:${VARNISH_ADMIN_LISTEN_PORT} \
-t ${VARNISH_TTL} \
-p thread_pool_min=${VARNISH_MIN_THREADS} \
-p thread_pool_max=${VARNISH_MAX_THREADS} \
-p thread_pool_timeout=${VARNISH_THREAD_TIMEOUT} \
-u varnish -g varnish \
-S ${VARNISH_SECRET_FILE} \
-s ${VARNISH_STORAGE} \
-n v"
```
####Basic Varnish Configuration

```
To invalidate cached objects in Varnish, begin by adding an ACL to your Varnish configuration. This ACL 
determines which IPs are allowed to issue invalidation requests. Let’s call the ACL invalidators. 

acl invalidators {
    "localhost";
        # Add any other IP addresses that your application runs on and that you
	    # want to allow invalidation requests from. For instance:
	        # "192.168.1.0"/24;

		}

Cache invalidation is a process in a computer system whereby entries in a cache are deleted.

#To configure Varnish for handling PURGE requests:
A purge is what happens when you pick out an object from the cache and discard it along with its variants.
Usually a purge is invoked through HTTP with the method PURGE.

sub vcl_recv {
    if (req.method == "PURGE") {
            if (!client.ip ~ invalidators) {
	                return (synth(405, "Not allowed"));
			        }
				        return (purge);
					    }
					    }

If your invalidation content is dynamic, you can purge it by varnishadm command also :

varnishadm 'ban req.http.host == www.xyz.com && req.url ~ "invalidation content"

#If you want to invalidate cached objects by forcing a refresh add the following to your Varnish configuration:

sub vcl_recv {
    if (req.http.Cache-Control ~ "no-cache" && client.ip ~ invalidators) {
            set req.hash_always_miss = true;
	        }
		}

#To configure Varnish for handling BAN requests:
You can think of bans as a sort of a filter on objects already in the cache. You ban certain content from being 
served from your cache. You can ban content based on any metadata we have. A ban will only work on objects already 
in the cache, it does not prevent new content from entering the cache or being served.

sub vcl_recv {

    if (req.method == "BAN") {
            if (!client.ip ~ invalidators) {
	                return (synth(405, "Not allowed"));
			        }

				            ban("obj.http.X-Host ~ " + req.http.X-Host
					                    + " && obj.http.X-Url ~ " + req.http.X-Url
			             + " && obj.http.content-type ~ " + req.http.X-Content-Type
      									                );
      
	                return (synth(200, "Banned"));
												    }
												    }

		    sub vcl_backend_response {

     # Set ban-lurker friendly custom headers
	 set beresp.http.X-Url = bereq.url;
           set beresp.http.X-Host = bereq.http.host;
														}
			sub vcl_deliver {

#Add the following to your Varnish configuration to enable cache tagging:



sub vcl_recv {

    if (req.method == "BAN") {
            if (!client.ip ~ invalidators) {
	                return (synth(405, "Not allowed"));
			        }

				        if (req.http.X-Cache-Tags) {
					            ban("obj.http.X-Host ~ " + req.http.X-Host
						                    + " && obj.http.X-Url ~ " + req.http.X-Url
                   + " && obj.http.content-type ~ " + req.http.X-Content-Type
	              + " && obj.http.X-Cache-Tags ~ " + req.http.X-Cache-Tags
											                );
							        } else {
				    	            ban("obj.http.X-Host ~ " + req.http.X-Host
					              + " && obj.http.X-Url ~ " + req.http.X-Url
		                 + " && obj.http.content-type ~ " + req.http.X-Content-																	                )												        }

																							        return (synth(200, "Banned"));
																								    }
																								    }

																								    sub vcl_backend_response {

																								        # Set ban-lurker friendly custom headers
																									    set beresp.http.X-Url = bereq.url;
																									        set beresp.http.X-Host = bereq.http.host;
																										}

																										sub vcl_deliver {

																										    # Keep ban-lurker headers only if debugging is enabled
																										        if (!resp.http.X-Cache-Debug) {
																											        # Remove ban-lurker friendly custom headers when delivering to client
																												        unset resp.http.X-Url;
																													        unset resp.http.X-Host;
																														        unset resp.http.X-Cache-Tags;
																															    }
																															    }
																								#Cleaning the Cookie Header:

In the examples above, an unaltered Cookie header is passed to the backend to use for determining the user context hash.
However, cookies as they are sent by a browser are unreliable. For instance, when using Google Analytics, cookie values are 
different for each request. Because of this, the hash request would not be cached, but multiple hashes would be generated 
for one and the same user.

To make the hash request cacheable, you must extract a stable user session id. 
																								sub vcl_recv {
																								    # ...

																								        set req.http.cookie = ";" + req.http.cookie;
																									    set req.http.cookie = regsuball(req.http.cookie, "; +", ";");
																									        set req.http.cookie = regsuball(req.http.cookie, ";(PHPSESSID)=", "; \1=");
																										    set req.http.cookie = regsuball(req.http.cookie, ";[^ ][^;]*", "");
																										        set req.http.cookie = regsuball(req.http.cookie, "^[; ]+|[; ]+$", "");

																											    # ...
																											    }

																								#Debugging:

Configure your Varnish to set a custom header (X-Cache) that shows whether a cache hit or miss occurred. 
This header will only be set if your application sends an X-Cache-Debug header:

																								sub vcl_deliver {
																								    # Add extra headers if debugging is enabled
																								        # In Varnish 4 the obj.hits counter behaviour has changed, so we use a
																									    # different method: if X-Varnish contains only 1 id, we have a miss, if it
																									        # contains more (and therefore a space), we have a hit.
																										    if (resp.http.X-Cache-Debug) {
																										            if (resp.http.X-Varnish ~ " ") {
																											                set resp.http.X-Cache = "HIT";
																													        } else {
																														            set resp.http.X-Cache = "MISS";
																															            }
																																        }
																																	}
```

#### Varnish Commands: 
```
#varnishadm 

to check the server status: sick/healthy
 varnishadm backend.list | grep -i servername

invalidate the content
varnishadm 'ban req.http.host == www.xyz.com && req.url ~ "invalidation content" 

 varnish-cli important commands:
 backend.set_health <backend_pattern> [auto|healthy|sick]
 ban.list : Start the Varnish cache process.
 status : Check status of Varnish cache process.

 varnishadm -T 127.0.0.1:8080 -S /etc/varnish/secret backend.set_health server auto
 manually sick and healthy the servers.

#varnishhist:
varnishhist is actually extremely useful to get an overview of the overall status of your backend servers and varnish

The pipes (|) are requests served from the cache whereas the hash-signs (#) are requests to the backend. The X axis
is a logaritmic scale for request times.

#varnishstat:
varnishstat can be used in two modes, equally useful. If run with only "varnishstat" you will get a continously 
updated list of the counters that fit on your screen. If you want all the values for indepth analysis you can 
however use "varnishstat -1" for the current counter values.

The very first row is the uptime of varnish. This instance had been restarted 1 day and 2h 44 mins before screenshot

The counters below that are the average hitrate of varnish. The first row is the timeframe and the second is the average 
hitrate. If varnishstat is kept open for longer the second timeframe will go up to 100 seconds and the third to 1000 seconds

As for the list of variables below the values correspons to total value, followed by current value and finally the average
value. Some rows that are apparently interesting would be

cache_hit/cache_miss to see if you have monumental miss storms

Relationship between client_conn and client_req to see if connections are being reused. In this case there's only 
API traffic where very few connections are kept open. So the almost 1:1 ratio is to be seen as normal.

Also relationship between s_hdrbytes and s_bodybytes is rather interesting as you can see how much of your bandwith
is actually being used by the headers. So if s_hdrbytes are a high percentage of your s_bodybytes you might want to 
consider if all your headers are actually necesary and useful.

#varnishtop
Varnishtop is a very handy tool to get filtered information about your traffic. Especially since alot of high-traffic
varnish sites do not have access_logs on their backend servers - this can be of great use.

See what requests are most common to the backend servers.
varnishtop -i txurl

See what useragents are the most common from the clients
varnishtop -i RxHeader -C -I ^User-Agent

See what user agents are commonly accessing the backend servers, compare to the previous one to find clients that are commonly causing misses.
varnishtop -i TxHeader -C -I ^User-Agent

See what cookies values are the most commonly sent to varnish.
varnishtop -i RxHeader -I Cookie

See what hosts are being accessed through varnish. Will of course only give you useful information if there are several hosts behind your
varnish  instance.
varnishtop -i RxHeader -I '^Host:'

See what accept-charsets are used by clients
varnishtop -i RxHeader -I '^Accept-Charset'

#varnishlog
varnishlog is yet another powerful tool to log the requests you want to analyze. It's also very useful without parameters to develop 
your vcl and see the exact results of your changes in all it's verbosity.

One useful example for listing all details about requests resulting in a 500 status:
varnishlog -b -m "RxStatus:500"

#varnishncsa
varnishncsa is a handy tool for producing apache/ncsa formatted logs. This is very useful if you want to log the requests to varnish 
and analyze them with one of the many availalable log analyzers that reads such logs, for instance awstats.

```
