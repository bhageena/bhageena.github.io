---
layout: post
title: "Motivation, Terms and Tools for Distributed Systems"
categories: isays
modified:
excerpt: "Sincere and honest development of one's potential -- it does not apply only on us but also on the things we build. When demand goes up the supply must also match, failing which can be chaotic. Capacity for expansion and growth is vital for survival of not only ours but also of our creations. We need to ensure that our software is scalable"
tags: []
image:
  feature:
readtime: true
---

## Distributed Systems: *Why*

>Ever since I was a child I have had this instinctive urge for expansion and growth. To me, the function and duty of a quality human being is the sincere and honest development of one's potential. -- Bruce Lee

Sincere and honest development of one's potential -- it does not apply only on us but also on the things we build. When demand goes up the supply must also match, failing which can be chaotic. Capacity for expansion and growth is vital for survival of not only ours but also of our creations. We need to ensure that our software is scalable.

Scalability is the capability of a system, network, or process to handle a growing amount of work, or its potential to be enlarged to accommodate that growth.
While there are various dimensions of scalability, the type of scalability I want to discuss is Load Scalability, it's the ability for a system to easily expand and contract its resource pool to accommodate heavier or lighter loads or number of inputs. Alternatively, the ease with which a system or component can be modified, added, or removed, to accommodate changing load.

Methods of adding more resources for a particular application fall into two broad categories - Horizontal and Vertical Scaling. So if you need to scale your system either you can do vertical scaling which means, you add more memory CPU and hard drive to an existing host or you do a horizontal scaling which is to keep one host small but instead add another host.

Vertical scaling can be expensive and also there is a limitation of how much memory and CPU you can add to a single host on the other hand in horizontal scaling you can infinitely keep adding more hosts so obviously horizontal scaling is more preferred than vertical scaling. Welcome to the world of distributed systems!

[Andrew Tannenbaum](https://en.wikipedia.org/wiki/Andrew_S._Tanenbaum) defines a distributed system as a collection of independent computers that appear to its users as one computer. Distributed Systems such as Cassandra Cluster act like a single system but they in fact are composed of many computers. Distributed Systems have three characteristics for something to qualify as a distributed system -- concurrency of components, lack of a global clock, and independent failure of components. But everything has a flip side and here, you have to deal with all the distributed system challenges like data synchronization‎, security, exception handing, logging.

Even though concurrent applications need not be distributed, often distributed systems are inherently concurrent so that opens up new frontier of problems - deadlocks, race conditions, byzantine failure, runtime differences and so on affect all systems that involve asynchronous coordination.

But the benefits of a distributed system outweighs the shortcomings by a huge margin. Lets take a look at terminology and the available tools to facilitate distributed systems and to overcome its limitations.  

## Distributed Systems: *Terms*

> It's clear enough that you are making some distinction in what you said, that there is some nicety of terminology in your words. I can't quite follow you.
-- Flann O'Brien

It's undeniably important to understand the meaning of technical jargons and the concept behind them in order to understand true nature of the distributed systems. You should read more about them for more insights.

### CAP Theorem
It's also known as Brewer's theorem, named after computer scientist Eric Brewer. CAP stands for Consistency Availability Partition Tolerance, consistency says that your read has the most recent write, availability says that you will get a response back it might be the most recent write or might not be the most recent write. Partition tolerance says that between two nodes you could be dropping network packets. You can only achieve two of these three tasks. You have to have partition tolerance because no distributed system is safe from network failures, so basically you're choosing between consistency or availability there are traditional relational databases they choose consistency over availability, which means, they could be less available but their data is always consistent. On the other hand NoSQL databases are prepared for availability over consistency if you choose to configure it so.

### ACID and BASE
ACID stands for Atomicity Consistency Isolation and Durability while BASE stands for Basically Available Soft state Eventual consistency. ACID is used more in terms of relational databases and BASE is used more for NoSQL database and you need to understand the references because once you start using more NoSQL databases you need to understand which part of acid properties you're willing to sacrifice.

### Partitioning or Sharding
Let's say you have trillions of records and there is no way you can store the trillions of records in one node of a database so you need to store them in many different nodes of a database and that's where sharding comes into the picture. How do you shard or split this data such that every node of a database is responsible for some parts or some of the records of those trillions of writers. One technique used heavily is consistent hashing and you definitely need to know how consistent hashing works, what are some of the advantages which consistent hashing brings to the table.

### Locking: Optimistic and Pessimistic
Let's say you are doing a database transaction and in the optimistic locking you do not acquire any locks but when you are ready to commit your transaction at that point you check to see if no other transaction updated the record which you are working. On another hand on pessimistic locking, you acquire all the locks beforehand and then you commit your transaction both of them have their advantages and disadvantages. You need to understand when to use which of this locking and in what scenario.

### Consistency: Strong and Eventual
Strong consistency means your reads will always see the latest write while the eventual consistency means that your reads will see some write and eventually it will see the latest write. Strong consistency is widely used in relational databases. In NoSQL database you have to decide if you want strong versus the eventual consistency and the benefit of the eventual consistency is that it provides higher availability and this all goes back to the CAP Theorem.

### Database: Relational and NoSQL
These days I see that most of the people prefer to use NoSQL database and that's fine but do not discard relational database just yet. Remember relational database provides all this nice acid properties. NoSQL database scales a little bit better and has higher availability so depending on the situation depending on the problem, try to see which one of the two fits. Four types of NoSQL database -- the first one is key value, so these are the simplest kind where you have a key and a value. It stores this key value pair into the database. The second one is wide column database. In such database our row can have many different formats, many different kinds of columns and it can also have many columns. Third is document based database, in this kind of DB, you have a semi structured data or if you have an XML or JSON data then you would use document based NoSQL service. The final one is graph based, let's suppose you have entities and  you have edges or relationship between those entities so you have a graph. The graph based NoSQL database is used to hold that graph.

### Caching
Caching is a technique widely used in computing to increase performance by keeping frequently accessed or expensive data in memory. Its used to speed up your request, if you know that some data is going to be accessed more frequently, then store it in the cache so that it can be accessed quickly. Caching are of two types -- first one is if every node does its own caching so the cache data is not shared between nodes. The second one is where the cache data is shared between different nodes. If you're in caching you have to consider few things -- first, cache cannot be the source of truth and second, cache data has to be pretty small because cache tends to keep all the data in memory and third, you have to consider some of the eviction policies around cache.

### Data Centers Racks and Host
You should be aware how the data center is architected or how data centers are arranged. Data centers have racks and racks have hosts, so you have to understand that what is the latency between talking across racks or cross hosts or what are the worst-cases that can happen if a rack goes down or if the entire data center goes down. Then we have CPU memory hard drive and network bandwidth all of these are limited resources so when you design your system you need to consider how do you work around these limitations and how do you improve the throughput latencies and scale your system along these limited resources.

### Random and Sequential Read and Write
Every time you need to access a block on a disk drive, the disk actuator arm has to move the head to the correct track, the time taken in this process is called seek time, then the disk platter has to rotate to locate the correct sector, this is called rotational latency. Reading or writing on a disk is usually slow but sequential reads and writes are actually amazing on the disk. So you should design your system around sequential reads and writes. Try to avoid random reads and writes which are order of magnitude slower than sequential reads and writes for that disk.

### HTTP/HTTP2/WebSocket
HTTP is the request reply kind of architecture between client and server, pretty much the entire web runs on HTTP. HTTP2 does some of the improvements on the deficiencies of HTTP like it can do multiple requests over a single connection. Then we have WebSocket which is fully bi-directional connected communication between client and server so it would be good to know some of the differences between them and some of the inner workings.

### IPv4/IPv6
IPv4(Internet Protocol Version 4) has 32-bit addresses and IPv6 has 128-bit addresses. IPv6 is often referred to as the next generation Internet standard and has been under development since the mid-1990s. IPv6 was born out of concern that the demand for IP addresses would exceed the available supply. IPv4 can only support total of 2^32 addresses, we are running out of IPv4 addresses so the world is migrating towards IPv6 and it's good to know some of the details around that and also how does the IP routing works.

### TCP/UDP
Both TCP and UDP are protocols used for sending bits of data — known as packets — over the Internet. TCP is connection oriented reliable connection while UDP is unreliable connection so if you are sending some document then TCP is preferable, if you're doing streaming of video then you are better off using UDP.

### TCP/IP Model
TCP/IP is a suite of protocols used by devices to communicate over the Internet and most local networks. It is named after two of it’s original protocols—the Transmission Control Protocol (TCP) and the Internet Protocol (IP). There are four layers of TCP/IP model and it's good to know what each layer does.

### DNS Lookup
It's Domain Name Server lookup, so if you type www.google.com in your browser, the request goes to the DNS which does a translation of this address into an IP address so it's good to know how that how it work and what is the hierarchy around them, how they do caching.

### HTTP and TLS
TLS is transport layer security so it is used to secure communication between client and server both in terms of privacy and data integrity and when used with HTTP it pretty much becomes HTTPS. Next is public key infrastructure, which is used to manage your public key and your digital certificates. Certificate authority is a trusted entity which tells you if the public key is from the correct party. e.g. if you type www.google.com in a browser and if this is going over HTTPS then you will get a public key back and certificate authority tells you that this public key is definitely coming from Google and not coming from a third party who has had between you and Google.

### Symmetric and Asymmetric encryption
Asymmetric encryption is computationally more expensive so it should be used to send small amount of data. Preferably use a symmetric key otherwise. An example of asymmetric encryption is public private key encryption while example of symmetric encryption is AES.

### Load Balancers
They sit in the front of a service and delegate the client requests to one of the nodes behind the service this delegation could be based on round-robin basis or the load average on the nodes behind that service. Load balancers can operate at L4 or L7 and these are the levels of OSI model. so L4 load balancer considers both client and destination IP addresses and port numbers to do the routing while at L7 which is an HTTP level it uses HTTP URI to do the routing most of the load balancers operate at level7.

### CDN and edge
A content delivery network (CDN) refers to a geographically distributed group of servers which work together to provide fast delivery of Internet content. It's is a network of servers linked together with the goal of delivering content as quickly, cheaply, reliably, and securely as possible. A primary purpose of a CDN edge server is to store content as close as possible to a requesting client machine, thereby reducing latency and improving page load times. CDN edge servers store (cache) content in strategic locations in order to take the load off of one or more origin servers.

### Bloom Filters and Count-min sketch
Bloom Filters and Count-min sketch are space efficient probabilistic based data structure. Bloom filter is used to decide if an element is a member of set or not it can have false positives but it will never have false negative so if your design can tolerate false positive you should consider using bloom filter because it's very space efficient. Count-min sketch is a similar data structure but it is used to count the frequency of events. Let's suppose you have millions of events and you want to keep track of top events then you can consider using count-min sketch instead of giving the count of all the events so for a fraction of space it will give an answer which will be close enough to the actual answer with some error rate.

### Paxos consensus over distributed hosts
Paxos is a family of protocols for solving consensus in a network of unreliable processors. Consensus is the process of agreeing on one result among a group of participants. This problem becomes difficult when the participants or their communication medium may experience failures. Paxos also has reputation as being fantastically difficult to understand so it's not mandatory to know it's internals but knowing what it is, is desirable.   

### Design Patterns and Object Oriented Design
Design patterns like factory methods and singleton are good to know while for object oriented design, abstractions and inheritance are some of the things you should be knowing.

### Virtual Machines and Containers  
Virtual machines are a way of giving you an operating system on top of a shared resource such that you feel like you are the exclusive owner of this hardware, while in reality that hardware is shared between different isolated operating systems. Container is a way of running your applications and its dependencies in an isolated environment, containers have become extremely important and they run a lot in the production environment these days.

### Publisher Subscriber or Queue
Some publisher publishes a message to a queue a subscriber receives that message from the queue. This pattern has become extremely important in the system design these days and you should definitely use them whenever you have an opportunity. One thing to remember is that customer facing requests should not be directly exposed to a pub sub system.

### MapReduce
It is used to do distributed and parallel processing of big data. Map is filtering and sorting the data and reduce is summarizing the data and this is something which is very important if you're working in big data.

### Multithreading, Concurrency, Tasks, Synchronization
These are all very important to know in the world of multi-threading, some modern programming languages comes with these things built in, while other languages like C you have to depend on the platform specific platform implementations.


Now we know some terms and concepts behind related to distributed system, next we'll have a look at the some of the tools widely used, this list by no means is exhaustive can it can be a good starting point. I urge you to go deeper and beyond what I have listed here in terms as well as tools sections.

## Distributed Systems: *Tools*

> The tools we use reinforce the behavior; the behavior reinforces the tool. Thus, if you want to change your behavior, change your tools.
– Adam Jacob, CTO, Chef

### Cassandra
Cassandra is a wide column based highly scalable database, it's used for different use cases like simple key value storage or for storing time series data or just your more traditional rows with many columns. Cassandra can provide both eventual and strong consistency. Under the hood it uses consistent hashing to shard your data and also uses gossiping to keep all the nodes informed about the cluster.

### MongoDB or Couchbase
MongoDB stores documents in BSON format. Documents that are semantically similar are grouped into a collection. In Couchbase, JSON documents are stored in buckets. However, unlike MongoDB's collection, a bucket usually does not contain only similar documents. In a way, it is similar to the concept of a database in RDBMS. MongoDB favors usability, whereas Couchbase tends to be more tunable.

### Memcached and Redis
Both tools are powerful, fast, in-memory data stores that are useful as a cache. Both can help speed up your application by caching database results, HTML fragments, or anything else that might be expensive to generate. These are distributed cache and hold the data in memory. Memcached is a simple volatile cache server. It allows you to store key/value pairs where the value is limited to being a string up to 1MB. Redis is more powerful, more popular, and better supported than memcached.

### Zookeeper
Its a centralized configuration management tool. It is also used for things like leader election and distributed locking. Zookeeper scales very well for the reads but does not scale that well for the writes. Since zookeeper keeps all their in memory so you cannot store way too much data in the zookeeper. So if you want to store small amount of data which would be highly available and which has tons of read then zookeeper is what you should be using.

### Kafka
Its a fault tolerant highly available queue using publisher subscriber or streaming application depending on your use case it can deliver message exactly once and also it keeps all the message ordered inside of partition alpha topping

### Nginx and HAProxy
These are load balancers and are very efficient for ings for an example nginx can manage thousands or even tens of thousands of connection from a client from a single instance

### Solr and Elasticsearch
Both of them are search platform on top of you see both of them are highly available very scalable and fault tolerant search platform and they do provide things like full-text search.

### Blobstore
If you have a big picture or a big file and you want to store it somewhere on the cloud then blobstore can be used. A very popular blob store is Amazon S3, which is provided as a part of AWS platform.

### Docker
Docker is a container technology that allows a developer to package up an application with all of the parts it needs. It's an open platform for developers and sysadmins to build, ship, and run distributed applications, whether on laptops, data center VMs, or the cloud.

### Kubernetes
Kubernetes is an open-source system for automating deployment, scaling, and management of containerized applications. It groups containers that make up an application into logical units for easy management and discovery.

### Hadoop
It's used for distributed storage and processing of dataset of big data using the MapReduce programming model. It consists of computer clusters built from commodity hardware. Hadoop services provide for data storage, data processing, data access, data governance, security, and operations.

### CoreOS
CoreOS is a powerful Linux distribution built to make large, scalable deployments on varied infrastructure simple to manage. Based on a build of Chrome OS, CoreOS maintains a lightweight host system and uses Docker containers for all applications.

### Vagrant
Vagrant enables users to create and configure lightweight, reproducible, and portable development environments. It is an open-source software product for building and maintaining portable virtual software development environments, e.g. for VirtualBox, Hyper-V, Docker, VMware, and AWS

### AWS
Amazon Web Services is a subsidiary of Amazon.com that provides reliable, scalable and on-demand cloud computing platforms on a paid subscription basis. It provides a plethora of services in computing, networking, content delivery, mobile services, deployment and analytics.

### Puppet and Chef
Puppet and Chef have both evolved significantly, Puppet is commonly deployed in a client/server configuration with managed nodes periodically synchronizing their configurations with the server. Reporting and other information is sent by the clients back to the server for aggregate analysis and processing. Chef uses martial arts as a metaphor for DevOps, specifically Kung-fu. At the basic level, Chef is a tool for automation, provisioning and configuration management. The platform is made up of the following components

### Saltstack and OpenStack
OpenStack is an open source implementation of cloud computing, potentially at very large scale. However, it has many moving parts and is complex to operate. SaltStack provides scalable and secure orchestration for OpenStack.

----
