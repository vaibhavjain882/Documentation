### Setup and Configure DNS in Windows Server 2012:

#### Install DNS Server Role in Server 2012

1. In the Server Manager console, click Add roles and features.
2. On the Before you Begin page, click Next.
3. On the Select installation type page, ensure that Role-based or feature-based installation is selected and click Next.
4. On the Select destination server page, ensure that the correct server is selected, and then click next.
5. On the Select server roles page, select DNS Server.
6. When the Add Roles and Features Wizard displays, click Add Features, and then click Next.
7. On the Select features page, click Next.
8. On the DNS Server page, click Next.
9. On the Confirm installation selections page, click Install.
10. On the Installation progress page, when the Installation succeeded message displays, click Close.

###Some key definitions:

client -> root server -> .com server -> google.com server ->  
####Caching-only DNS Server:
Whether you use the server manager or Windows PowerShell, the DNS server has no zone configured right after the installation. Without zones, you could still use this server as a caching-only DNS.

A caching-only DNS server is not authoritative for any domain. It receives name resolution requests and generates either an iterative query to the root hints on the Internet or it forwards the request to another DNS server when configured as a forwarder. The caching-only DNS server caches the answers before sending them back to the DNS client; that way, the next time a name resolution query comes asking for the same name, the caching-only DNS server responds from its own cache instead of generating another iterative query or forwarding the request.

#### DNS Zones:

#### Forward Lookup Zones:
Forward lookup zones resolve host names to IP addresses, and they answer to name queries by replying with the corresponding IP addresses that match the names in those queries.

#### Reverse Lookup Zones:
Reverse lookup zones resolve IP addresses to domain names. When an IP address is part of the query, the reverse lookup zone returns the corresponding host name. Reverse lookup zones host SOA, NS, and pointer (PTR) resource records. Separate reverse lookup zones must be created for IPv4 and IPv6 on Windows Server 2012. It is possible to run a DNS infrastructure without configuring reverse lookup zones, but some important functionalities will be missing as a result and the service will generate numerous warnings and error messages.

Reverse lookup zones can be used to fight spam. Spammers use open relays (SMTP servers) on the internet to send their massive unsolicited emails and hide their identity. A mail server can perform reverse lookups to try to detect open relays; this would allow the application of traffic filtering from those open relays which can prevent or minimize unwelcome spam.

Another important benefit of reverse lookup zones is that their data is frequently used to validate forward zone information. For example, if the forward lookup specifies that support.mycompany.com is resolved to 172.16.0.8, you can use a reverse lookup to confirm that 172.16.0.8 is really associated with support.mycompany.com.

#### Recursive and Iterative DNS Queries:

A DNS server answers recursive and iterative DNS queries. Recursive queries require a fully resolved IP address from the DNS server. These types of queries are usually originated from DNS clients to a DNS server. After receiving a recursive query, a DNS server will either respond with the corresponding IP address or generate a negative response. The DNS server will not refer the DNS client to another DNS server to continue its query.

Iterative queries are typically initiated by a DNS server after receiving a name resolution query that it cannot answer using its local database or its cache lookups. In this case, the DNS server uses an iterative query to send the same question to another DNS server. If the other DNS server has the answer, it responds with the IP address for the requested name, but if it doesn’t know the answer, it sends back a referral to the DNS servers that are accountable for the domain being queried. This process goes on until an authoritative DNS is found or a time-out condition is reached.

#### Root Hints and Iterative Queries:

Earlier I mentioned that when installing DNS on Windows Server 2012 a list of Internet root server addresses (root hints) is preloaded by default. These root hints point to the top level DNS servers on the internet. These servers hold intelligence about the top level domains like .com, .org, .net, .edu, etc. When installing the DNS service, this information is copied from the cache.dns file which is by default located on the %windir%\system32\dns directory.

The root hint servers are not configured to respond to recursive queries, and DNS servers only send iterative queries to the root hints. This may be a little confusing for some people because to stop a DNS server from sending queries to the root hints, the option Do Not Use Recursion For This Domain must be selected. However, recursion on a DNS server and recursive queries are two different things.

When recursion is enabled on a DNS server, it means that the server may send DNS queries to its configured root hints or to other DNS servers when acting as a forwarder. Recursive queries are name resolution requests made to a DNS server in which the requester asks the DNS server to provide a complete yes or no answer. The DNS server cannot respond with a referral for the requestor to contact another DNS server.

#### Primary Zone:
A DNS server can read and write data on a primary zone. This is possible because the DNS server stores the master copy of the zone data either in a text file or in the Active Directory database if the DNS is installed on a domain controller. 
A DNS server is authoritative for the records that it holds on a primary zone. That means if the DNS server receives a name resolution query that includes the domain name on the primary zone, the DNS server will respond with a yes or no answer. The authoritative DNS will not forward that name resolution query to any other DNS server.

#### Secondary Zone:
A DNS server can read, but not write data on a secondary zone. A secondary zone is a copy of a primary zone that another DNS server hosts. The information on the secondary zone is obtained and updated via zone transfers from another server. A read-only text file is used to store the information locally; secondary zones cannot be store in the active directory database. A DNS server is authoritative for the records that it holds on a secondary zone.

#### Stub Zone:

A stub zone is a limited copy of a zone that consists of the following records: start of authority (SOA) resource records, name server (NS) records, and host name (A) records. These records are used to identify the zone’s authoritative DNS servers. The DNS server holding the stub zone is not authoritative for that zone. When this DNS server receives a name resolution query, it needs to ask one of the authoritative DNS servers from the stub zone.

#### Active directory integrated zone:

Active directory integrated zones can be configured only on domain controllers that are also DNS servers. This is a primary zone with its data stored in the active directory database.

#### Benefits of using active directory integrated zones:

Secure dynamic updates: Dynamic updates allow DNS clients to register their resource records in the DNS database automatically without manual intervention. This feature is available on standard primary zones; however, only active directory integrated DNS zones can be configured for secure dynamic updates. This means that you can set permissions on the zone to allow only authorized computers to register in the DNS database.

Secure replication topology: There is no need to configure zone transfer on Active Directory integrated zones the way you have to do it with standard primary zones and secondary zones. With Active Directory integrated zones, the DNS data is transferred automatically as part of the active directory replication. All AD replication is encrypted by default.

Increase resilience: There is no single point of failure when you have multiple domain controllers holding active directory integrated zones. Each domain controller has a read/write copy of the DNS zone; this allows changes and automatic updates performed on any domain controller to be replicated across the domain or the forest using the powerful active directory replication engine.

Security permissions: Like any other active directory object, you can delegate administration and apply individual permissions to zones, and resource records by modifying the access control list (ACL) on the zone.

#### DNS Security Extensions (DNSSEC):

 The DNS protocol does not have any built-in capabilities to ensure authentication or check the integrity of the DNS information that is exchanged between DNS servers or delivered to DNS clients. This known vulnerability can be exploited by attackers who may be able to hijack the name resolution activity when users are trying to reach a website on the internet. One purpose of the attacker could be to take control of the process and redirect the user’s browser to a phony scam website where the user is asked to enter personal information like username, password, credit card, bank account or social security numbers. DNSSEC uses Public Key Infrastructure (PKI) certificates with the DNS protocol to allow the DNS servers to validate DNS responses. With DNSSEC, an administrator can digitally sign a DNS zone, which is a way to digitally sign all the records within that zone.

When a DNS query is issued for a resource record in a signed zone, a digital signature is returned with the response so that validation can be performed. The validation process ensures that the data has not been modified or tampered with and can be trusted by the DNS resolver.

Two important components of a DNSSEC implementation are Trust Points and Name Resolution Policy Table:
#### Trust Points:
These provide a way to share the public key used to validate the RRSIG record’s digital signature with other trusted DNS servers. If the DNS server is running on a domain controller, trust points can be stored in the forest directory partition in Active Directory Domain Services (AD DS) from which they can be replicated to all DNS servers running on domain controllers in the forest.

#### Name Resolution Policy Table (NRPT):

It lists zones or namespaces that perform DNSSEC queries and those that do not. It is possible to use either group policy or Windows PowerShell to configure the NRPT to require that DNSSEC validation be performed on DNS responses on select namespaces.


1.  click the Tools menu and select DNS.

2.  Select the DNS server to manage, then click the Action menu, and select Configure a DNS Server. This brings up the Configure a DNS Server wizard.

3. There are three options here. You can either: configure a forward lookup zone only, create forward and reverse lookup zone, or configure root hints only.

4. Now, you choose whether this server will maintain the zone, or if this server will have a read-only copy of the DNS records from another server.

5. Next enter your zone name. If this is your first DNS server, then this needs to be the root zone name for your entire organization. For example, my zone name might be arcticllama.com. If however, this server will be authoritative only for a subset, and other DNS servers will be responsible for other zones, then the name will need to reflect that. For example, us.arcticllama.com would be the zone name for just the American part of my vast corporate empire :) Click next when you have entered the name. 

6. Now, you need to choose the file name where the DNS records will be stored. The default filename is to add a .dns extension to the name of the zone you chose in the previous window. Unless you have a corporate policy stating otherwise, stick with the convention to make things easier on yourself down the line. 

7. Next you select how this server will respond to Dynamic Updates. Although there are three choices here, only two should actually be used in production. Select the first option to allow only secure dynamic updates if you are integrating your DNS with Active Directory. Select do not allow dynamic updates if your DNS is not integrated with Active Directory and you don't want to allow dynamic updates. Do not allow unsecured dynamic updates unless you really know what you are doing and have a very good reason for doing so. 

8. Up next is the option to configure forwarders. If your DNS server ever gets a query for which it has no record, it can forward that request on to another DNS server to see if it has the answer.

9. For example, in order to provide name resolution for internet connectivity, you can input your ISP name servers here, or use a DNS provider such as OpenDNS. You can (and should) have more than one server listed in case a DNS server is unreachable for some reason. The order forwarders are listed in is the order they are tried, so place your faster and most reliable forwarder at the top of the list. 

10. Click Next and your DNS server is now configured and ready for use.
