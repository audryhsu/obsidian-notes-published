---
tags:
  - books
  - engineering
  - designing-data-intensive-applications
aliases: 
publish: true
---
# Reliable, Scalable, and Maintainable Applications

Today's world, we use many different types of data systems woven together to create applications. We use our application code to stich them together.
- Just think about how many different systems are used in a theorhetical system design interview -- DB, DB replicas, cache, web server, queues, CDNs, etc.

Some systems may share superficial similarities (a database and a message queue can both store some data), but their distinct characteristics make them optimized for a *particular* use case:
- data access patterns (reads vs writes)
- durability of data (in memory vs on disk)
- create different performance characteristics

> [!NOTE] 
> **The modern engineer has evolved from application developer to a data systems engineer**
>When she combines application code + underlying general purpose components -->  a service interface/API that abstracts away many implementation details from the client. 


In this 🌎, a modern service is *distributed* across many components and geographical locations. 

How can we design data intensive systems that are **reliable, scalable, and maintainable? **

## Reliability
*Reliability* - systems work correctly even when fault occurs (hardware, software, or humans). 

You can't prevent all faults but you can design systems that handle them well
- earlier days, we used **hardware redundancy** to protect against hardware faults
- with commoditized hardware managed by someone else (cloud), we should ==aim to design systems that can tolerate the loss of entire machines using software fault-tolerance techniques==, too
	- Infrastructure as code -> docker, terraform, cdks
- software faults are especially heinous (cue SVU voice over) because they are hard to detect and lie dormant over a long period of time.
- human errors are probably the most common. How can we minimize?
	- well-designed abstractions, APIs, and admin interfaces make it easy to *do the right thing* and discourage *doing the wrong thing*.
	- provide sandbox environments
	- Test thoroughly at all levels
	- make it easy to roll back changes, or easy to roll out new code gradually
	- observability 


## Scalability
*Scalability* means having strategies for keeping up good performance even when load increases.
- response times can be measure in percentiles
![Pasted image 20231104201549.png](../images/Pasted%20image%2020231104201549.png)

> [!NOTE] Why do we care about the highest percentiles in performance measurements?
> Generally, *averages* like average response rate don't tell you as much about the systems performance as the outliers (*p95, p99, p999*)
> >High percentiles (*tail latencies*) are especially important in backend services that are called multiple times as part of service a single end-user request. Even if calls are made in parallel, the request still needs to wait for the slowest of the parallel calls to complete. 
> >Even if only a small percentage of calls are slow, the chance of getting a slow call increases if an end-user request requires multiple backend calls, resulting in a higher proportion of end-user requests being slow (*tail latency amplification*)
> ![Pasted image 20231104202337.png](../images/Pasted%20image%2020231104202337.png)
> >  

## Maintainability
*Maintainability* - make life easier for engineering and ops teams

Key questions for assessing a systems maintainability:
1. is it easy for new engineers to understand?
2. is it easy to run operationally?
3. is it extensible/evolvable for the future?


![ddia_chapter_1.svg](../images/ddia_chapter_1.svg)
%%[🖋 Edit in Excalidraw](../images/ddia_chapter_1.excalidraw), and the [dark exported image](ddia_chapter_1.dark.svg)%%