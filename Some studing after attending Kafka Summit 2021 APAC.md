# Some studing after attending Kafka Summit 2021 APAC

## Before Writing

This document is something not so technologically oriented. It will record my personal own understanding about Apache Kafka and the concept of "Data in motion". 

## Summary

​    So the first idea I want to mention is "Data in motion". This phrase is the most important idea in this Summit. What is "Data in motion"? Why this idea persuade me to believe that it will be the main principle of data Industry? And How it will change our word and life? I would like to explain this idea to start this report.

## My personal understanding of "Data in motion"

​	Let us talk about the definition of data in motion, before we start dive into "Data in motion", I want to have a quick introduction about "Data at rest". There is the wikipedia's definition:

> **Data at rest** in [information technology](https://en.wikipedia.org/wiki/Information_technology) means data that is housed physically on [computer data storage](https://en.wikipedia.org/wiki/Computer_data_storage) in any digital form (e.g., [cloud storage](https://en.wikipedia.org/wiki/Cloud_storage), [file hosting services](https://en.wikipedia.org/wiki/File_hosting_service), [databases](https://en.wikipedia.org/wiki/Database), [data warehouses](https://en.wikipedia.org/wiki/Data_warehouse), [spreadsheets](https://en.wikipedia.org/wiki/Spreadsheet), archives, tapes, off-site or cloud backups, [mobile devices](https://en.wikipedia.org/wiki/Mobile_device) , etc.).

So basically, "Data at rest" is one strategy to process data which means to store data into a data base, like a poor, then do some batch operation to process these data. So there is a delay between data in poor and data in process. This delay means these data will not be used in certain amont of time and just store in the data base. Such delay can be varient from a few second to several weeks even months. This is a good way to discover the value behind these huge data by using data mining and actually it works petty well during these years. Companies like Google, Microsoft and Amazon are all the supporters of "Data at rest", they build huge cloud services to handle these operations and do the data processing jobs for their own business, like the machine learning, it requires tons of data to train the model and fit the real usage in the world. However, there is no one size fits all solution in data science field, the processing latency (also you can say the delay) in "Data at rest" limits it's performance in real time use case, like the real time chat, video streaming platform, online gaming  industry and so on. The demand of read time processing is huge and  "Data at rest" cannot fit this require. So we must find a way to solve this problem, this is why "Data in motion" come to the stage. 

​	There is something that I need to mention before I talk about "Data in motion". The relationship between "Data at rest" and "Data in motion" is similar to parallel. There are many reasons for why "Data in motion" sounds like a new idea. And I am not quiet sure about these reasons, part of the reason might due to some lack of hardware and algorithm support. Or, may be there was no such great demand a few decade ago. ~~I will do some further research and try to find out the answers.~~ Jun Rao, the co-founder of confluent and the main creator of Apache Kafka, from his keynote presentation, I get some inspiration of the answers. Part of my assumptions are right, so, about twenty years ago, people treated data in a "Data at rest" way and built some message system to handle some real-time requirements. So, these message system has limited capacity and hard to scale. This solution works very well at that period since there was no so much data to store and process. However, just about ten years ago, LinkedIn realized two things. First, they found that processing data in a "Data in motion" way is more efficient than "Data at rest," because that they only need to build some trigger conditions and wait data to come in their data base. This action makes their services are more customer centered. Another things is "Leverage all Digitized Events". This means transactional events are equally important with non-transactional events. And non-transactional events' number are much higher than transactional event. By these two realizations, LinkedIn bursted its value about ten times. With LinkedIn's success, others follow up and "Data in motion" comes to stage again. However, the old solution does not work for LinkedIn's new ideas. They designed a new system which is called "**Kafka**" and I will introduce it later. (2021/7/30) The only discrepancy between these two ideas is just field each of them focus on different field and there is some overlap between them. Such as the idea of micro batch is one solution to solve the real time data processing in "Data at rest". Also, "Data in motion" can be used in to store huge amount of data.

​	There is the definition of "Data in motion" given by Laura Fitzgibbons. You can visit this article for some deep dive into the security view about "Data in motion". (https://whatis.techtarget.com/definition/data-in-motion)

> Data in motion, also referred to as data in transit or data in flight, is digital information that is in the process of being transported between locations either within or between computer systems. 

so, as I mentioned above, the core idea of "Data at rest" is to passively collect tons of data and do some operations after a while. However, the core idea behind "Data in motion" is to set some conditions when a message or event (you can also say data.) fits these conditions then store it into system and push this message to the corresponding services and process it. (There is almost no delay between receiving data and pushing data.) You can image that there is a long "data river" on the internet. "Data in motion" tries to process these "Data" in the river which means while these data is still flowing or in transport. When the long river reachs it's end then we process these data, we call it "Data at rest". (2021/8/2) Briefly speaking, "Data in motion" is more likely to select data which is necessary for our requirement. "Data at rest" is to collect all data and try to find something fits our requirement. 

2021/7/29

## Some basic about Apache Kafka 

​	This section will have multiple source from the official site of Apache Kafka. You can visit its site https://kafka.apache.org/ to have a full version of Apache Kafka. Space time mass

### The problems Apache Kafka tries to solve

Space time mass

### Event streaming

> ​	Event streaming is the digital equivalent of the human body's central nervous system. It is the technological foundation for the 'always-on' world where businesses are increasingly software-defined and automated, and where the user of software is more software.
>
> ​	Technically speaking, event streaming is the practice of capturing data in real-time from event sources like databases, sensors, mobile devices, cloud services, and software applications in the form of streams of events; storing these event streams durably for later retrieval; manipulating, processing, and reacting to the event streams in real-time as well as retrospectively; and routing the event streams to different destination technologies as needed. Event streaming thus ensures a continuous flow and interpretation of data so that the right information is at the right place, at the right time.
>

### Main components 

​	Current, Apache Kafka composes of 4 parts, producer, customer, Kafka cluster and zookeeper. As it sounds like, producer and customer are the input and output entry of the whole system. Kafka cluster's job is to store data in the event-streaming format. Last, zookeeper is used to manage the job of each broker in the cluster and the community of Apache Kafka is going to replace it by a method called KRaft. This method is a modified version of Raft and you can read [KIP-500](https://cwiki.apache.org/confluence/display/KAFKA/KIP-500%3A+Replace+ZooKeeper+with+a+Self-Managed+Metadata+Quorum#KIP500:ReplaceZooKeeperwithaSelfManagedMetadataQuorum-MetadataasanEventLog) to get more information about it. 	

## Extra Reading

### Transactional events and Non-Transactional events

​			By searching in Google, we can get the defination of transactional events (information) form http://www.computerbusinessresearch.com/.			

> ​		Transactional information is all the information contained within a business unit. The primary purpose of transactional information is to support day-to-day operations of the unit. Examples of transactional information include: sales receipt, packing slip, purchase confirmation, etc. 

So, the definition of non-transactional events are very simple. It is just those "Non"-transactional events. Let me give some examples. (From https://stackoverflow.com/questions/18884728/difference-between-transactional-and-non-transactional)

> Non -Transactional (These information are relevant to enterprise for longer duration than Transactional Data.)
>
> - Customer: Name, Preferences
> - Product: Name, Hierarchy
> - Site/Location: Addresses
> - Account: Contracts Detail
>
> Transactional (Has a Time Dimension, and becomes historical once the transaction is complete)
>
> - Financial: orders, invoices, payments

## Reference

https://www.youtube.com/watch?v=zDlM67GIqTY (Jun Rao's keynote presentation)

https://www.youtube.com/watch?v=ALX23Vaqp70 (Closing Keynote of Kafka Summit APAC 2021)

https://kafka.apache.org/intro

https://stackoverflow.com/questions/18884728/difference-between-transactional-and-non-transactional

http://www.computerbusinessresearch.com/

https://en.wikipedia.org/wiki/Data_at_rest

https://whatis.techtarget.com/definition/data-in-motion

https://www.manageengine.com/device-control/data-in-motion.html

https://cwiki.apache.org/confluence/display/KAFKA/KIP-500%3A+Replace+ZooKeeper+with+a+Self-Managed+Metadata+Quorum#KIP500:ReplaceZooKeeperwithaSelfManagedMetadataQuorum-MetadataasanEventLog