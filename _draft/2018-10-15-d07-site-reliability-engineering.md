---
layout: article
title: Designs - Site Reliability Engineering
key: d07-site-reliability-engineering
categories: Designs
tags: SRE
---

## What is SRE?

In general, an SRE team is responsible for the _availability, latency, performance, efficiency, change management, monitoring, emergency response, and capacity planning_ of their service(s).


Data is transferred to and from an RPC using protocol buffers,4 often abbreviated to “protobufs,” which are similar to Apache’s Thrift. Protocol buffers have many advantages over XML for serializing structured data: they are simpler to use, 3 to 10 times smaller, 20 to 100 times faster, and less ambiguous.

Extreme reliability comes at a cost: maximizing stability limits how fast new features can be developed and how quickly products can be delivered to users, and dramatically increases their cost, which in turn reduces the numbers of features a team can afford to offer. Further, users typically don’t notice the difference between high reliability and extreme reliability in a service, because the user experience is dominated by less reliable components like the cellular network or the device they are working with.


### HBase Features

- Linear and modular scalability.
- Strictly consistent reads and writes.

  HBase is not an "eventually consistent" DataStore. This makes it very suitable for tasks such as high-speed counter aggregation.
  
- Automatic and configurable sharding of tables
  HBase tables are distributed on the cluster via regions, and regions are automatically split and re-distributed as your data grows.
- Automatic failover support between RegionServers.
- Convenient base classes for backing Hadoop MapReduce jobs with Apache HBase tables.
- Easy to use Java API for client access.
- Block cache and Bloom Filters for real-time queries.
- Query predicate push down via server side Filters
- Thrift gateway and a REST-ful Web service that supports XML, Protobuf, and binary data encoding options
- Extensible jruby-based (JIRB) shell
- Support for exporting metrics via the Hadoop metrics subsystem to files or Ganglia; or via JMX

### Difference Between HBase and Hadoop/HDFS

HDFS is a distributed file system that is well suited for the storage of large files. Its documentation states that it is not, however, a general purpose file system, and does not provide fast individual record lookups in files. HBase, on the other hand, is built on top of HDFS and provides fast record lookups (and updates) for large tables. This can sometimes be a point of conceptual confusion. HBase internally puts your data in indexed "StoreFiles" that exist on HDFS for high-speed lookups. See the Data Model and the rest of this chapter for more information on how HBase achieves its goals.

### Data Model


Within a table, data is partitioned by 1-column row key in lexicographical order, where topically related data is stored close together to maximize performance. The design of the row key is crucial and has to be thoroughly thought through in the algorithm written by the developer to ensure efficient data lookups.

### Cassandra vs. HBase

Cassandra is a ‘self-sufficient’ technology for data storage and management, while HBase is not. The latter was intended as a tool for random data input/output for HDFS, which is why all its data is stored there. Besides, HBase uses Zookeeper as a server status manager and the ‘guru’ that knows where all metadata is (to avoid immediate cluster failures, when the metadata-containing master goes down). Consequently, HBase’s complex interdependent system is more difficult to configure, secure and maintain.

Cassandra is good at writes, whereas HBase is good at intensive reads. Cassandra’s weak spot is data consistency, while HBase’s pain is data availability, although both try to mitigate the adverse consequences of these problems. Also, both don’t stand frequent data deletes and updates.

But the main difference between applying Cassandra and HBase in real projects is this. Cassandra is good for ‘always-on’ web or mobile apps and projects with complex and/or real-time analytics. But if there’s no rush for analysis results (for instance, doing data lake experiments or creating machine learning models), HBase may be a good choice. Especially if you’ve already invested in Hadoop infrastructure and skill set.





# Reference Resources
