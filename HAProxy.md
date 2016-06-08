###HAProxy
HAProxy(High Availability Proxy) is an open source load balancer which can load balance any TCP service. It is particularly suited for HTTP load balancing as it supports session persistence and layer 7 processing.

####Few basic Configuration :
Let's start with block by block:
```
global
    log 127.0.0.1 local0 notice
        maxconn 2000
	    user haproxy
	        group haproxy
```

The log directive mentions a syslog server to which log messages will be sent. On Ubuntu rsyslog is already installed and running but it doesn't listen on any IP address. We'll modify the config files of rsyslog later.

The maxconn directive specifies the number of concurrent connections on the frontend. The default value is 2000 and should be tuned according to your VPS' configuration.

The user and group directives changes the HAProxy process to the specified user/group. These shouldn't be changed.

```
defaults
    log     global
        mode    http
	    option  httplog
	        option  dontlognull
		    retries 3
		        option redispatch
			    timeout connect  5000
			        timeout client  10000
				    timeout server  10000
				    timeout http-keep-alive 10s
				     maxconn                 3000

```

We're specifying default values in this section. The values to be modified are the various timeout directives. The connect option specifies the maximum time to wait for a connection attempt to a VPS to succeed.

The client and server timeouts apply when the client or server is expected to acknowledge or send data during the TCP process. HAProxy recommends setting the client and server timeouts to the same value.

The retries directive sets the number of retries to perform on a VPS after a connection failure.

The option redispatch enables session redistribution in case of connection failures. So session stickness is overriden if a VPS goes down.


```
listen appname 0.0.0.0:80
    mode http
        stats enable
	    stats uri /haproxy?stats
	        stats realm Strictly\ Private
		    stats auth A_Username:YourPassword
		        stats auth Another_User:passwd
			    balance roundrobin
			        option httpclose
				    option forwardfor
				        server lamp1 10.0.0.1:80 check
					    server lamp2 10.0.0.2:80 check
```

This contains configuration for both the frontend and backend. But If oyu want to write frontend and backend follow configuration manual of haproxy. Few important configuration directives of backend and frontend are given below :

```
capture request  header :  In order to log headers and cookies, the capture parameters must be set in the frontend.
acl blacklist src -f /etc/haproxy/blacklist-ips : blacklist IP's 
acl whitelist src -f /etc/haproxy/whitelist-ips : Whitelist IP's
acl conn_rate_abuse sc0_conn_rate gt 100 : If Someone hits a lot intensionaly, block them.
stick-table :  this setting is meant to be use when you want a connection to be load balanced between nodes but you 
want the connection to stay on the node it goes to for a timeframe you configure. stick-tables are indexed on what allows 
to recognize a client, they are often also used to store extra information such as per-client statistics
ACL: ACLs are used to test some condition and perform an action (e.g. select a server, or block a request) 
       ex: acl url_blog path_beg /blog
 http-request redirect scheme https if !{ ssl_fc } : redirect httpd traffic to https if it is not https.
use_backend : use this in frontend section to configure backend for particular acl.
X-Forwarded-Proto: https header to indicate that the request was sent over SSL. If this header is missing, the
       request is redirected to the https:// flavor of the URL.
balance: round robin, least connections.

```

#### Some theoritical Facts about HAproxy:
a TCP proxy : it can accept a TCP connection from a listening socket,
    connect to a server and attach these sockets together allowing traffic to
        flow in both directions;

an HTTP reverse-proxy (called a "gateway" in HTTP terminology) : it presents
    itself as a server, receives HTTP requests over connections accepted on a
        listening TCP socket, and passes the requests from these connections to
	    servers using different connections.

an SSL terminator / initiator / offloader : SSL/TLS may be used on the
    connection coming from the client, on the connection going to the server,
        or even on both connections.

a TCP normalizer : since connections are locally terminated by the operating
    system, there is no relation between both sides, so abnormal traffic such as
        invalid packets, flag combinations, window advertisements, sequence numbers,
	    incomplete connections (SYN floods), or so will not be passed to the other
	        side. This protects fragile TCP stacks from protocol attacks, and also
		    allows to optimize the connection parameters with the client without having
		        to modify the servers' TCP stack settings.

an HTTP normalizer : when configured to process HTTP traffic, only valid
    complete requests are passed. This protects against a lot of protocol-based
        attacks. Additionally, protocol deviations for which there is a tolerance
	    in the specification are fixed so that they don't cause problem on the
	        servers (eg: multiple-line headers).

a content-based switch : it can consider any element from the request to
    decide what server to pass the request or connection to. Thus it is possible
        to handle multiple protocols over a same port (eg: http, https, ssh).

a server load balancer : it can load balance TCP connections and HTTP
    requests. In TCP mode, load balancing decisions are taken for the whole
        connection. In HTTP mode, decisions are taken per request.

a traffic regulator : it can apply some rate limiting at various points,
    protect the servers against overloading, adjust traffic priorities based on
        the contents, and even pass such information to lower layers and outer
	    network components by marking packets.

an HTTP compression offloader : it can compress responses which were not
    compressed by the server, thus reducing the page load time for clients with
        poor connectivity or using high-latency, mobile networks.

Note: You can put HAProxy Server in front of any caching server bcz it doesn't cache things but provide smart load balancing.

#####Backend: 
A backend is a set of servers that receives forwarded requests. Backends are defined in the backend section of the HAProxy configuration. In its most basic form, a backend can be defined by:

which load balance algorithm to use
a list of servers and ports
```
backend web-backend
   balance roundrobin
      server web1 web1.yourdomain.com:80 check
         server web2 web2.yourdomain.com:80 check
```
#####Frontend: 
A frontend defines how requests should be forwarded to backends. Frontends are defined in the frontend section of the HAProxy configuration. Their definitions are composed of the following components:

a set of IP addresses and a port (e.g. 10.1.1.7:80, *:443, etc.)
ACLs
use_backend rules, which define which backends to use depending on which ACL conditions are matched, and/or a default_backend rule that handles every other case.

#####Health Check
HAProxy uses health checks to determine if a backend server is available to process requests. This avoids having to manually remove a server from the backend if it becomes unavailable. The default health check is to try to establish a TCP connection to the server i.e. it checks if the backend server is listening on the configured IP address and port.

If a server fails a health check, and therefore is unable to serve requests, it is automatically disabled in the backend i.e. traffic will not be forwarded to it until it becomes healthy again. If all servers in a backend fail, the service will become unavailable until at least one of those backend servers becomes healthy again.

For certain types of backends, like database servers in certain situations, the default health check is insufficient to determine whether a server is still healthy.

```
backend server1 {
  .host = "server1.example.com";
    .probe = {
             .url = "/";
	              .interval = 5s;
		               .timeout = 1 s;
			                .window = 5;
					         .threshold = 3;
						     }
						       }
						       backend server2 {
						          .host = "server2.example.com";
							     .probe = {
							              .url = "/";
								               .interval = 5s;
									                .timeout = 1 s;
											         .window = 5;
												          .threshold = 3;
													     }
													      }
```

