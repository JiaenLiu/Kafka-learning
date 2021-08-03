# Tencent's Kafka

## Overall structure

<img src="/Users/liujiaen/Library/Application Support/typora-user-images/image-20210802094108452.png" alt="image-20210802094108452" style="zoom:50%;" />

Overall, the key concept of Tencent's logic is to get the logs and push them into two directions. One direction is to using these logs or events to drive its own business service. Another way is to store these information into Tencent's own data base.

Here are some corresponding phrases of the acronyms.

ODS: operational data store

DWD: Data Warehouse Design

DWS: Data Warehouse System

ADS: Alternate data stream

## Some detail

### Tencent's demand

- Support for very large-scale clusters
- Topic partition needs to support lossless scaling down
- Low latency and high availability 

### Original federal Kafka design

![image-20210802104246135](/Users/liujiaen/Library/Application Support/typora-user-images/image-20210802104246135.png)

![image-20210802104309103](/Users/liujiaen/Library/Application Support/typora-user-images/image-20210802104309103.png)

![image-20210802104315582](/Users/liujiaen/Library/Application Support/typora-user-images/image-20210802104315582.png)

![image-20210802104331562](/Users/liujiaen/Library/Application Support/typora-user-images/image-20210802104331562.png)



### Advantages and drawbacks of Cluster federation

#### Advantages

- The topic partition can achieve lossless expansion and contraction
- Disaster tolerance can be achieved at the sub-cluster level

#### Drawbacks

- Cannot support a large number of topics
- Proxy service needs to be implied to all Kafka protocols (this is a quiet huge job.) and hard to add new feature from the Apache Kafka community.
- Latency, due to the proxy.

### Tencent's Kafka

![截屏2021-07-28 下午2.13.32](/var/folders/ys/sscdp_311pb7nw2dnwktjffnv775jp/T/com.apple.Preview/com.apple.Preview.PasteboardItems/截屏2021-07-28 下午2.13.32.png)

![截屏2021-07-28 下午2.15.28](/var/folders/ys/sscdp_311pb7nw2dnwktjffnv775jp/T/com.apple.Preview/com.apple.Preview.PasteboardItems/截屏2021-07-28 下午2.15.28.png)

![image-20210802104709935](/Users/liujiaen/Library/Application Support/typora-user-images/image-20210802104709935.png)

![image-20210802104719426](/Users/liujiaen/Library/Application Support/typora-user-images/image-20210802104719426.png)

![image-20210802104733910](/Users/liujiaen/Library/Application Support/typora-user-images/image-20210802104733910.png)

![image-20210802104740213](/Users/liujiaen/Library/Application Support/typora-user-images/image-20210802104740213.png)

### My personal view about Tencent's Kafka

​	So, this is a quiet stright forward way to achieve the needs of Tencent's own business. Personally, I think the most interesting part of this solution is the way they handle the management of Kafka cluster. As they said, the using a new Kafka cluster to manage the remain parts of Kafka clusters. This is great solution while do not replace zookeeper. The idea behind this behavior is some sort of "scaling". And it is very similar to the lever cache strategy in computer system. When the number of partitions reachs a certain number, it is very struggle for zookeeper to manage these brokers and manage whole Kafka system. However, zookeeper does give us a lot of convenient functions and it works petty well when the size of Kafka is not too big. So, the key step of Tencent's solution is using a modified Kafka cluster which do not have  broker on it, manages the metadata. And to release the load of zookeeper, they decide to group all clusters by region. Any cross-region topic partition is not banned. 

​	Also, Tencent's engineers decide to remove the proxy layer. This action makes it easier for Kafka to integrate some new feature of the community. But I am not sure about how they achieve this. For next step, Tencent may continue to contribute on KIP-500 to replace zookeeper and  I will do some study about it. 

​	KIP-500, it is the document about the replacement of zookeeper in Kafka and I found some great reasons about why zookeeper cannot fit today's requirement.

> ## Metadata as an Event Log
>
> We often talk about the benefits of managing state as a stream of events. A single number, the offset, describes a consumer's position in the stream. Multiple consumers can quickly catch up to the latest state simply by replaying all the events newer than their current offset. The log establishes a clear ordering between events, and ensures that the consumers always move along a single timeline.
>
> **However, although our users enjoy these benefits, Kafka itself has been left out.** We treat changes to metadata as isolated changes with no relationship to each other. When the controller pushes out state change notifications (such as LeaderAndIsrRequest) to other brokers in the cluster, it is possible for brokers to get some of the changes, but not all. Although the controller retries several times, it eventually give up. This can leave brokers in a divergent state.
>
> **Worse still, although ZooKeeper is the store of record, the state in ZooKeeper often doesn't match the state that is held in memory in the controller.** For example, when a partition leader changes its ISR in ZK, the controller will typically not learn about these changes for many seconds. There is no generic way for the controller to follow the ZooKeeper event log. Although the controller can set one-shot watches, the number of watches is limited for performance reasons. When a watch triggers, it doesn't tell the controller the current state-- only that the state has changed. By the time the controller re-reads the znode and sets up a new watch, the state may have changed from what it was when the watch originally fired. If there is no watch set, the controller may not learn about the change at all. In some cases, restarting the controller is the only way to resolve the discrepancy.
>
> Rather than being stored in a separate system, metadata should be stored in Kafka itself. This will avoid all the problems associated with discrepancies between the controller state and the Zookeeper state. Rather than pushing out notifications to brokers, brokers should simply consume metadata events from the event log. This ensures that metadata changes will always arrive in the same order. Brokers will be able to store metadata locally in a file. When they start up, they will only need to read what has changed from the controller, not the full state. This will let us support more partitions with less CPU consumption.

​	There are some deployment and configuration improvements after removing zookeeper.

> ## Simpler Deployment and Configuration
>
> ZooKeeper is a separate system, with its own configuration file syntax, management tools, and deployment patterns. This means that system administrators need to learn how to manage and deploy two separate distributed systems in order to deploy Kafka. This can be a daunting task for administrators, especially if they are not very familiar with deploying Java services. Unifying the system would greatly improve the "day one" experience of running Kafka, and help broaden its adoption.
>
> Because the Kafka and ZooKeeper configurations are separate, it is easy to make mistakes. For example, administrators may set up SASL on Kafka, and incorrectly think that they have secured all of the data traveling over the network. In fact, it is also necessary to configure security in the separate, external ZooKeeper system in order to do this. Unifying the two systems would give a uniform security configuration model.
>
> Finally, in the future we may want to support a single-node Kafka mode. This would be useful for people who wanted to quickly test out Kafka without starting multiple daemons. Removing the ZooKeeper dependency makes this possible.

​	State mismatch.

> ![img](https://cwiki.apache.org/confluence/download/attachments/123898922/a.png?version=1&modificationDate=1564694752000&api=v2)
>
> Note that this diagram is slightly misleading. Other brokers besides the controller can and do communicate with ZooKeeper. So really, a line should be drawn from each broker to ZK. However, drawing that many lines would make the diagram difficult to read. Another issue which this diagram leaves out is that external command line tools and utilities can modify the state in ZooKeeper, without the involvement of the controller. As discussed earlier, these issues make it difficult to know whether the state in memory on the controller truly reflects the persistent state in ZooKeeper.

## Definition

Data warehouse:

> In [computing](https://en.wikipedia.org/wiki/Computing), a **data warehouse** (**DW** or **DWH**), also known as an **enterprise data warehouse** (**EDW**), is a system used for [reporting](https://en.wikipedia.org/wiki/Business_reporting) and data analysis and is considered a core component of [business intelligence](https://en.wikipedia.org/wiki/Business_intelligence).[[1\]](https://en.wikipedia.org/wiki/Data_warehouse#cite_note-1) DWs are central repositories of integrated data from one or more disparate sources. They store current and historical data in one single place[[2\]](https://en.wikipedia.org/wiki/Data_warehouse#cite_note-rjmetrics-2) that are used for creating analytical reports for workers throughout the enterprise.[[3\]](https://en.wikipedia.org/wiki/Data_warehouse#cite_note-spotlessdata-3)

Apache Flink:

> **Apache Flink** is an [open-source](https://en.wikipedia.org/wiki/Open-source_software), unified [stream-processing](https://en.wikipedia.org/wiki/Stream_processing) and [batch-processing](https://en.wikipedia.org/wiki/Batch_processing) [framework](https://en.wikipedia.org/wiki/Software_framework) developed by the [Apache Software Foundation](https://en.wikipedia.org/wiki/Apache_Software_Foundation). The core of Apache Flink is a distributed streaming data-flow engine written in [Java](https://en.wikipedia.org/wiki/Java_(programming_language)) and [Scala](https://en.wikipedia.org/wiki/Scala_(programming_language)).[[2\]](https://en.wikipedia.org/wiki/Apache_Flink#cite_note-2)[[3\]](https://en.wikipedia.org/wiki/Apache_Flink#cite_note-3) Flink executes arbitrary [dataflow](https://en.wikipedia.org/wiki/Dataflow_programming) programs in a [data-parallel](https://en.wikipedia.org/wiki/Data_parallelism) and [pipelined](https://en.wikipedia.org/wiki/Pipeline_(software)) (hence [task parallel](https://en.wikipedia.org/wiki/Task_parallelism)) manner.[[4\]](https://en.wikipedia.org/wiki/Apache_Flink#cite_note-4) Flink's pipelined runtime system enables the execution of [bulk/batch](https://en.wikipedia.org/wiki/Batch_processing) and stream processing programs.[[5\]](https://en.wikipedia.org/wiki/Apache_Flink#cite_note-5)[[6\]](https://en.wikipedia.org/wiki/Apache_Flink#cite_note-6) Furthermore, Flink's runtime supports the execution of [iterative algorithms](https://en.wikipedia.org/wiki/Iterative_method) natively.[[7\]](https://en.wikipedia.org/wiki/Apache_Flink#cite_note-7)
>
> Flink provides a high-throughput, low-latency streaming engine[[8\]](https://en.wikipedia.org/wiki/Apache_Flink#cite_note-8) as well as support for event-time processing and state management. Flink applications are fault-tolerant in the event of machine failure and support exactly-once semantics.[[9\]](https://en.wikipedia.org/wiki/Apache_Flink#cite_note-:2-9) Programs can be written in [Java](https://en.wikipedia.org/wiki/Java_(programming_language)), [Scala](https://en.wikipedia.org/wiki/Scala_(programming_language)),[[10\]](https://en.wikipedia.org/wiki/Apache_Flink#cite_note-10) [Python](https://en.wikipedia.org/wiki/Python_(programming_language)),[[11\]](https://en.wikipedia.org/wiki/Apache_Flink#cite_note-11) and [SQL](https://en.wikipedia.org/wiki/SQL)[[12\]](https://en.wikipedia.org/wiki/Apache_Flink#cite_note-12) and are automatically compiled and optimized[[13\]](https://en.wikipedia.org/wiki/Apache_Flink#cite_note-13) into dataflow programs that are executed in a cluster or cloud environment.[[14\]](https://en.wikipedia.org/wiki/Apache_Flink#cite_note-14)
>
> Flink does not provide its own data-storage system, but provides data-source and sink connectors to systems such as [Amazon Kinesis](https://en.wikipedia.org/wiki/Amazon_Web_Services#Analytics), [Apache Kafka](https://en.wikipedia.org/wiki/Apache_Kafka), [HDFS](https://en.wikipedia.org/wiki/HDFS), [Apache Cassandra](https://en.wikipedia.org/wiki/Apache_Cassandra), and [ElasticSearch](https://en.wikipedia.org/wiki/Elasticsearch).[[15\]](https://en.wikipedia.org/wiki/Apache_Flink#cite_note-:0-15)



## Reference

https://en.wikipedia.org/wiki/Data_warehouse

https://en.wikipedia.org/wiki/Apache_Flink

https://cwiki.apache.org/confluence/display/KAFKA/KIP-500%3A+Replace+ZooKeeper+with+a+Self-Managed+Metadata+Quorum#KIP500:ReplaceZooKeeperwithaSelfManagedMetadataQuorum-MetadataasanEventLog

https://kafkasummit.io/session-virtual/?v26dd132ae80017cdaf764437c30ebe6f10c1b1eeaab01165e44366654b368dfaeab6baf7e386a642ecb238989334530e=FFBAA5A18A1382F8FE99CE8FAA83B7B551583065287F0669212F8B00119C37FAA9EE6FB616077BAEAC2EC227B4DF7372&fromFollowed (You need to have an account to access this link. The name of this presentation is "**Enhancing Apache Kafka for Large Scale Real-Time Data Pipeline at Tencent**" and the speaker's pronunciation is really really bad. UPDATE: the website of Kafka summit is closed.)

