# Some studing after attending Kafka Summit 2021 APAC

## Summary

​    So the first idea I want to mention is "Data in motion". This phrase is the most important idea in this Summit. What is "Data in motion"? Why this idea convince me to believe that it will be the main principle of data Industry? And How it will change our word and life? I would like to explain this idea to start this report. 

​	Let us talk about the defination of data in motion, before we start dive into "Data in motion", I want to have a quick introduction about "Data at rest". There is the wikipedia's defination:

> **Data at rest** in [information technology](https://en.wikipedia.org/wiki/Information_technology) means data that is housed physically on [computer data storage](https://en.wikipedia.org/wiki/Computer_data_storage) in any digital form (e.g. [cloud storage](https://en.wikipedia.org/wiki/Cloud_storage), [file hosting services](https://en.wikipedia.org/wiki/File_hosting_service), [databases](https://en.wikipedia.org/wiki/Database), [data warehouses](https://en.wikipedia.org/wiki/Data_warehouse), [spreadsheets](https://en.wikipedia.org/wiki/Spreadsheet), archives, tapes, off-site or cloud backups, [mobile devices](https://en.wikipedia.org/wiki/Mobile_device) etc.).

So basically, "Data at rest" is one strategy to process data which means to store data into a data base, like a poor, then do some batch operation to process these data. So there is a delay between data in poor and data in process. This delay means these data will not be used in certain amont of time and just store in the data base. Such delay can be varient from a few second to several weeks even months. This is a good way to discover the value behind these huge data by using data mining and actually it works petty well during these years. Companies like Google, Microsoft and Amazon are all the supporters of "Data at rest", they build huge cloud services to handle these operations and do the data processing jobs for their own business, like the machine learning, it requires tons of data to train the model and fit the real usage in the world. However, there is no one size fits all solution in data science field, the processing latency (also you can say the delay) in "Data at rest" limits it's performance in real time use case, like the real time chat, video streaming platform, online gaming  industry and so on. The demand of read time processing is huge and  "Data at rest" cannot fit this require. So we must find a way to solve this problem, this is why "Data in motion" come to the stage. 

​	There is something that I need to mention before I talk about "Data in motion". The relationship between "Data at rest" and "Data in motion" is similar to parallel. The are many reasons for why "Data in motion" sounds like a new idea. And I am not quiet sure about these reasons, part of the reason might due to some lack of hardware and algorithm support. Or, may be there was no such great demand a few decade ago. I will do some futher reasearch and try to find out the answers. The only discrepancy between these two ideas is just field each of them focus on different field and there is some overlap between them. Such as the idea of micro batch is one solution to solve the real time data processing in "Data at rest". Also, "Data in motion" can be used in to store huge amount of data.

​	So, as I mentioned above, the core idea of "Data at rest" is to passively collect tons of data and do some operations after a while. However, the core idea behind "Data in motion" is to set some conditions when a message or event (you can also say data.) fits these conditions then store it into system and push this message to the corresponding services and process it. (There is almost no delay between receiving data and pushing data.) Briefly speaking, "Data in motion" is more likely to select data which is necessary for our requirement. "Data at rest" is to collect all data and try to find something fits our requirement. 

To be continued 

2021/7/29

​	