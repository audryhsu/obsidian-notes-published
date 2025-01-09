---
creation date: 2022-05-27 17:19
modification date: Friday 27th May 2022 17:19:40
tags:
  - books
publish: true
---
| Author           | Subject         | Goodreads  | Reviewed Date  |
| ---------------- | --------------- | ---------- | -------------- |
| Martin Kleppmann | [Scalability](../Scalability.md) | ⭕  Not Yet | [2022-05-27](2022-05-27.md) |
# Part I Foundations of Data System
 🔑 How to design system to achieve reliability, scalability, and maintainability in data intensive applications?
- [DDIA Chapter 1](./DDIA%20Chapter%201.md)
- [DDIA Chapter 2](./DDIA%20Chapter%202.md)
- [DDIA Chapter 4 Encoding and Evolution](./DDIA%20Chapter%204%20Encoding%20and%20Evolution.md)
# Part II Distributed Data
🔑 Why distribute data across multiple machines in a shared-nothing architecture?
- 👍 can use commodity servers, don't need special hardware (cost effective)
- 👍 **reduce latency** distribute across geographic regions to reduce latency
- 👍**scalability** - share load
- 👍high **availability** and fault tolerant (redundancy allows fail over)
- 👎increase complexity
- 👎network is the only way to communicate

Two main ways to distribute data across multiple nodes: [DDIA Chapter 5 Replication](./DDIA%20Chapter%205%20Replication.md) and [partitioning](Designing%20Data%20Intensive%20Applications.md#Partitioning%20Data%20(sharding)). These are often used in conjunction. 
- [DDIA Chapter 7 Transactions](DDIA%20Chapter%207%20Transactions.md)
- [DDIA Chapter 8](../DDIA%20Chapter%208.md)
- [DDIA Chapter 9 Consistency and Consensus](DDIA%20Chapter%209%20Consistency%20and%20Consensus.md)
# Part III Derived Data
Applications use a combination of datastores, indexes, caches, and analytic systems for moving data from one store to another. Challenges around integrating different data systems that are optimized for different data access patterns to form one coherent architecture. Integrating disparate systems is one of the most important challenges.

Your source of truth is your database. Other data sources that transform or manipulate the data into other points of view are said to be *derived data sources*. 
- [DDIA Chapter 10 Batch Processing](DDIA%20Chapter%2010%20Batch%20Processing.md)
- [DDIA Chapter 11 Stream Processing](DDIA%20Chapter%2011%20Stream%20Processing.md)
- [DDIA Chapter 12 Future of Data Systems](DDIA%20Chapter%2012%20Future%20of%20Data%20Systems.md)
# Chapter 6 Partitioning
Chapter Topics:
- approaches to partitioning datasets
- how indexing of data interacts with partitioning
- rebalacing partitions (+/- nodes)
- how databases route requests to right 

> [!NOTE] Partitioning data distributes load across multiple disks 
> Partitioning is *combined with replication** so copies of each partition are on multiple nodes for fault tolerance
>
> Ways to partition:
> - hash key - ensures even distribution among partitions
> 	- mongoDB and [Cassandra](../Cassandra.md) use MD5
> - key range (e.g. encyclopedia) - risk of skewed data, but nicely sorted
> - Hybrid - [Cassandra](../Cassandra.md) uses compound primary key based on several columns; first part of column is hashed (to determine partition), rest of columns used a concatenated index for sorting data

![Drawing_2024-01-29 09.26.11.excalidraw](../images/Drawing_2024-01-29%2009.26.11.svg)
%%[🖋 Edit in Excalidraw](../images/Drawing_2024-01-29%2009.26.11.svg)%%

## How to partition key-value data
### Partitioning by key range
🧠 like an encyclopedia!
- easy to do range scans since keys are sorted (e.g. fetch sensor data from January 2024)
- hot spots might occur (e.g. all writes for today go to same partition)
	- alleviate by prefixing timestamps by another key, like sensor id
### Partitioning by hash of key
🧠 partitions are assigned a range of hashes (instead of keys)
- similar strings -> hash function ->  even and random distribution across a number range
- mitigates hot spots

![Drawing_2024-01-29 09.56.35.excalidraw](../images/Drawing_2024-01-29%2009.56.35.svg)
%%[🖋 Edit in Excalidraw](../images/Drawing_2024-01-29%2009.56.35.svg)%%

What if workload is skewed because of a celebrity, hitting same key? 
- App code could "salt" the key by adding a random number to beginning or end of key to split the writes to a different partition.
- Downside: reads are split across x number of partitions, and you have to keep track of which keys were split

#### Unreliable Networks
- Nodes know nothing about each other (no shared memory), and rely solely on network communication.
- 
![Pasted image 20220528155334.png](../images/Pasted%20image%2020220528155334.png)

You don't need to be fault *tolerant*, you just need to know how your system responds to partitions/faults.

> [!NOTE]  Implementing Automatic Failovers are hard
> Network gives no guarantees when a packet will arrive (if at all). If you don't get a response, *it's impossible to know why*. 
> 
> To be able to implement automatic failover, you need to know when a failure happens. In a distributed system, it's impossible to distinguish a slow-to-answer from a dead node 💀
> 

Need to be able to detect faults automatically
- How does a load balancer know to stop sending traffic to a down node? Is it down, or just slow?
- How do you know when a leader in a leader-follower database cluster is dead?
- You can set a longer timeout (time user waits until error message) to know for sure if experiencing a fault
- Tradeoff: Short timeout detects fault faster, but higher probability of false positive
- false positives can be dangerous too if a request is half processed

Variability on packet delays is due to queuing, which happens at many stages of packet transfer:
- queuing at network switch
- queuing by [OS](./Operating%20System.md) if CPU cores are busy
- [TCP](../TCP.md) flow control -- limits rate of sending packets via queue



# Batch Processing
Batch processing is good for scanning over large portions of data. Different than online transaction processing (OLTP)
- building search indexes
- build ML systems like classifiers

###### Tools/Philosophies that showcase batch processing
- Unix philosophy - don't mutate input, write to new output
	- you can rerun a job as many times as you want
	- a job's output can be "piped" and connected as an input to another job
		- separates logic from wiring (i/o configurations) for resuability
	- many data analyses can be done in a few minutes using some combination of awk, sed, grep, sort, uniq, and xargs
- MapReduce batch processing algorithm in 2004 was a big deal, but less important nowadays
	-  programming framework with which you can write code to process large datasets in a distributed filesystem like Hadoop Distributed File System
	- each mapReduce job is independent from every other job.
	- mapper function iterates over each input to key-values
	- sort by keys
	- reducer function reduces groups of keys
	- ![Pasted image 20220603122519.png](../images/Pasted%20image%2020220603122519.png)
# Stream Processing
- event streams are a mechanism to manage data
- unbounded, incrementally processed (as opposed to batch data with fixed-size input)
- anti-polling
- databases aren't good at [event-driven architecture](../Event-driven%20Architecture.md) 

##### Transmitting event streams
- *events* are small, self-contained immutable objects containing details of something that happened
- events are generated once by a producer and potentially processed by multiple consumers
- related events are grouped in *topic* or *stream*

Messaging systems have several options for when producers send messages faster than consumers can process
	- producers apply backpressure (flow control) like Unix and UDP
	- drop messages (and possibly retry)
	- buffer in a queue

Message brokers (queues) are servers that centralize data so that clients can come and go.
- Up to broker to persist messages (either disk or in memory) until its read by consumer
- consumers send an `ACK` back to broker so broker knows to delete it
- brokers assume that queue will remain fairly short 

Two patterns when there are multiple consumers (can be combined)
1. Load balancing - each message is delivered to *one* consumer to process to spread the burden of processing messages
2. Fan out - message is delivered to *all* consumers

Challenges arise when a consumer in a load-balancing pattern crashes
- Broker will resend message and a different consumer will pick it up, but now messages can be out of order. 

![Pasted image 20220603114513.png](../images/Pasted%20image%2020220603114513.png)

