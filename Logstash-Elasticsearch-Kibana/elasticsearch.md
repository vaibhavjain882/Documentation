### What is elasticsearch

Elasticsearch is a distributed RESTful search engine built for the cloud. Elasticsearch is a search server based on Lucene. It provides a distributed, multitenant-capable full-text search engine with an HTTP web interface and schema-free JSON documents. Elasticsearch is developed in Java and is released as open source under the terms of the Apache License. Elasticsearch is the most popular enterprise search engine followed by Apache Solr, also based on Lucene. Features include:

Lucene: Apache Lucene is a free and open-source information retrieval software library.


```
Distributed and Highly Available Search Engine
Each index is fully sharded with a configurable number of shards.
Each shard can have one or more replicas.
Read / Search operations performed on either one of the replica shard.
Multi Tenant with Multi Types.
Support for more than one index.
Support for more than one type per index.
Index level configuration (number of shards, index storage, …).
Various set of APIs
HTTP RESTful API
Native Java API.
All APIs perform automatic node operation rerouting.
Document oriented
No need for upfront schema definition.
Schema can be defined per type for customization of the indexing process.
Reliable, Asynchronous Write Behind for long term persistency.
(Near) Real Time Search.
Built on top of Lucene
Each shard is a fully functional Lucene index
All the power of Lucene easily exposed through simple configuration / plugins.
Per operation consistency
# ESC 1.5.2, Lucene 4.10.2
Single document level operations are atomic, consistent, isolated and durable.
Open Source under the Apache License, version 2 (“ALv2”)
```

###Configuring ElasticSearch: 
elasticsearch.yml
```
#node.name : specifies the name of the server (node).
#cluster.name : value is used by the auto-discovery feature of Elasticsearch to automatically discover and associate Elasticsearch nodes to a cluster.

Note:  if you don't change the default value, you might have unwanted nodes, found on the same network, in your cluster.
node.master:The setting which determines the role of the server, Set it false if you want it to be slave.

Note:"Masters" are responsible for the cluster health and stability.  "master" will not store data or create indexes. 

#node.data: there are two cases in which you might wish not to store data on a node. One is when the node is a dedicated "master,". The other is when a 
node is used only for fetching data from nodes and aggregating results. In the latter case the node will act up as a "search load balancer".

#index.number_of_shards: determines into how many pieces (shards) the index will be split into.
#index.number_of_replicas: defines the number of replicas which will be distributed across the cluster. 

Note: Having more shards improves the indexing performance, while having more replicas makes searching faster.

#path.data:  determines the path where data is stored.
script.disable_dynamic: disable custom expressions, By crafting a custom malicious expression, an attacker might be able to compromise your environment.

network.host:  Set the address other nodes will use to communicate with this node. Ex: network.host: [_tun0_, _local_]

Note: "_local_", which configures Elasticsearch to also listen on all loopback devices. This will allow you to use the Elasticsearch HTTP API locally, 
from each server, by sending requests to localhost. If you do not include this, Elasticsearch will only respond to requests to host network.

discovery.zen.ping.unicast.hosts: you will need to configure an initial list of nodes that will be contacted to discover and form a cluster. 
Ex: discovery.zen.ping.unicast.hosts: ["10.0.0.1", "10.0.0.2", "10.0.0.3"]

bootstrap.mlockall: avoid excessive swapping is to configure Elasticsearch to lock the memory that it needs.
```

/etc/default/elasticsearch
```
ES_HEAP_SIZE: set it to about 50% of your available memory
MAX_LOCKED_MEMORY : =unlimited

MAX_OPEN_FILES:  set it to the limit you desire.

Note: check max open files: cat /proc/11708/limits | grep 'Max open files'
```

#### Few commands to access elasticsearch from browser:

```
curl http://localhost:9200/_nodes/process?pretty
curl -XGET localhost:9200/_cluster/settings?pretty
http://10.31.33.13:9200/_cat/health #to know the individual host status.
```
Split Brain: one or more nodes lose connectivity to the rest of the cluster, preventing what is known as a "split-brain" situation. it is important to set the minimum number of master-eligible nodes that need to be running for the cluster to function normally, which is sometimes referred to as quorum. 

### Plugins Of elasticsearch:

#### bigdesk:
It Shows JVM ()Heap, No-heap, GC ), thread pools, OS level Info (CPU, Memory, SWAp, Load average), HTTP and transport address and size, Indices (Search time per second, Search resuqests per seconds, Indexing request and time per second etc.) File system Information (Size, read and write operations).

#### head:
A data browser with a simple search function, a graphical query builder where you can click together search queries by selecting index, available fields, and query type and text, plus a JSON editor for formulating any HTTP request to be sent to the Elasticsearch server. Especially the latter provides some nice convenience features like a request history, repeated execution, or transforming the response JSON by applying a custom Javascript function.By default, it shows all documents.
You can see, flush, delete indices and it's data. YOu can find size of documents.

#### HQ:
The HQ plugin is a more recent one, and it easily has the sexiest website. HQ is a general-purpose plugin that offers various features, ranging from cluster, node, and index level metrics to query support for search and other REST calls. In the settings section, it allows you to specify different refresh intervals for different pages, making it possible to enable fine-grained sampling for certain data of interest while effectively disabling others. node diagnostics page which shows a table with metrics and statistics for each cluster node. What makes the feature special is that HQ applies thresholds to judge whether the values are acceptable, require a closer look, or are alarming, and then colors the table cells accordingly to make the information easily digestible. Furthermore, on mouseover it explains exactly how a certain table cell value is computed, which thresholds are applied, and why HQ thinks something is alarming. 

#### river-mongodb:
This plugin uses MongoDB or the TokuMX fork of MongoDB as datasource to store data in ElasticSearch. Filtering and transformation are also possible. You can delete, start the mongodb rivers.

#### kopf :
This is the enhanced version of head plugin. the various filters offered are highly useful: Nodes by name and type (client, data, master-eligible), indexes by name and state, hide special indexes created internally by Elasticsearch. An analysis feature that allows you to see the tokens generated for your document fields or your self-defined analyzers. Simple forms enabling you to use try advanced features like index aliases, percolation, warmup queries, or snapshot/restore. Many many text fields where you can dynamically change a particular cluster, node, or index setting, with helpful tooltips explaining what the particular field about. And, finally, Kopf offers a JSON editor for arbitrary REST calls, checking the entered JSON for validity as you type, and even formatting it once you submit the request.

NOTE: you can access these plugins in URL by http://hostname:9200/_plugin/plugin-name

#### ICU Analysis Plugins:
The ICU Analysis plugin integrates the Lucene ICU module into elasticsearch, adding extended Unicode support using the ICU libraries, including better analysis of Asian languages, Unicode normalization, Unicode-aware case folding, collation support, and transliteration

#### Installing Plugins
Core Elasticsearch plugins can be installed as follows:
```
sudo bin/plugin install [plugin_name]
```

Non-core plugins provided by Elasticsearch, or plugins provided by the community, can be installed as :
```
sudo bin/plugin install [org]/[user|component]/[version]
 ex: sudo bin/plugin install lmenezes/elasticsearch-kopf
 ```
#### Color Code for node's health:
```
Green = elasticsearch server is up
Red = elasticsearch server is down
Yellow = shards are allocating
```

#### How to import data into elasticsearch from mysql using JDBC.

1. Download and unzip the elasticsearch-jdbc library.
```
wget http://xbib.org/repository/org/xbib/elasticsearch/importer/elasticsearch-jdbc/2.1.0.0/elasticsearch-jdbc-2.1.0.0-dist.zip
Unzip this and go to it's bin directory.
```
2. Create a script to import data from mysql as follows :
```
#!/bin/bash
DIR="$( cd "$( dirname "${BASH_SOURCE[0]}" )" &amp;amp;amp;amp;&amp;amp;amp;amp; pwd )"
bin=${DIR}/../bin
lib=${DIR}/../lib
 
 echo '
 {
 "type" : "jdbc",
 "jdbc" : {
 "url" : "jdbc:mysql://172.17.0.101:3306/world",
 "user" : "root",
 "password" : "password",
 "sql" : "select anything as _id,anything.name, anything.rollno as roll number, from databasename;",
 "treat_binary_as_string" : true,
 "elasticsearch" : {
 "cluster" : "elasticsearch cluster name",
 "host" : "172.17.0.101",
 "port" : 9300
 },
 "max_bulk_actions" : 20000,
 "max_concurrent_bulk_requests" : 10,
 "index" : "index_name"
 }
 }
 ' | java \
 -cp "${lib}/*" \
 -Dlog4j.configurationFile=${bin}/log4j2.xml \
 org.xbib.tools.Runner \
 org.xbib.tools.JDBCImporter
  
```
3. Finally execute a query on elasticsearch to verify the indexes:
```
curl 'http://localhost:9200/_cat/indices?v'
```
4. Retrieve some data to verify
```
curl -XGET 'localhost:9200/index_name/_search?size=3&amp;amp;amp;pretty=true'
```

#### Import data into elasticsearch in mongodb by mongodb-river"

1. Index a collection from mongodb
```
curl -XPUT localhost:9200/_river/DATABASE_NAME/_meta -d '{
  "type": "mongodb",
    "mongodb": {
        "servers": [
	      { "host": "127.0.0.1", "port": 27017 }
	          ],
		      "db": "DATABASE_NAME",
		          "collection": "ACTUAL_COLLECTION_NAME",
			      "options": { "secondary_read_preference": true },
			          "gridfs": false
				    },
				      "index": {
				          "name": "ARBITRARY INDEX NAME",
					      "type": "ARBITRARY TYPE NAME"
					        }
						}'
```

2. Verify your indexes :
```
curl -XGET http://localhost:9200/_aliases
```
#### Basic Query in elasticsearch:
query for documents / rows with title field containing 'saurabh'
```
curl {endpoint}/_search?q=title:jones&size=5&pretty=true
```
Adding some data
```
 # Data (argument to -d) should be a JSON document
     curl -X POST  {endpoint} -d '{
           "title": "jones",
	         "amount": 5.7
		     }'
```

For complex queries you can use Json, python etc.

