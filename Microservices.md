---
creation date: 2022-04-10 14:56
aliases: 
tags: 🖥️📥
---

# [Microservices](Microservices.md)
---
Microservices describes a software architectural design style in which an application is made up of a suite of small, independently deployable services that communicate with each other. Microservices is rising in popularity against the status-quo, monolithic style.
##### Challenges of Monolith
Monolith's server-side logic is a single process that needs to be rebuilt and redeployed in its entirety when there is a change because processes are *tightly coupled* and run as a single service. 

Microservice stye is characterized by componentization via services, where a component is independently replaceable and upgradeable. Each application process is run as a service -- these services re built for business capabilities and perform a single function. 

![Pasted image 20220410150354.png](./images/Pasted%20image%2020220410150354.png)


### Should you monolith or microservice?
Microservices make sense just like manufacturing is profitable -- it happens when there is *scale*. Some say that you should start as a [monolith first](https://martinfowler.com/bliki/MonolithFirst.html) so you understand the complexity of your system and there's a more tangible milestone for modularizing to reduce that complexity. 

On the other hand, if your system ends up not being big or complex enough, you might be introducing unnecessary complexity by building microservices from Day 0.

#### Pros Cons of Microservices
👍easier to horizontally scale services independently and on a need-basis
👍higher code velocity - don't have CI/CD the entire monolith (e.g. testing)
👍can make technical decisions (e.g. choosing technologies) better suited for microservice use case

👎Traceability - harder to debug microservice architecture (why observability is hot)
👎Interservice dependencies - coordinate deployment changes if microservice A depends on microservice B
👎Not as resilient - network and latency between services instead of talking to each other in-memory
👎Versioning - update endpoints and maintain older versions

Think: modules and class objects vs mini-apps talking to each other

#### Solutions to Challenges
You can use all three if you want!
###### API Gateways 
☎️ receptionist - sends visitors to right service
- like a reverse proxy for the group of microservices
- load balances health checks, routing, authentication, SSL termination, logging, etc.
- first line defensive against public internet & offloads repetitive tasks
- stable API for clients - services implementation can change and client interface is consistent
- 👎single point of failure, one team maintains API
- 👎not efficient for large volumes of service-to-service traffic
![Pasted image 20220601213755.png](./images/Pasted%20image%2020220601213755.png)

###### Message Queues
- decouple sender and receiver -- don't need to health check, route, end point discovery, or load balance
- services pick relevant tasks from queue when they are ready
- auto-orchestration & scaling can be made based on message counts in queues 
- 👎not good at request/response communications by nature
- 👎buffered messaages can add latency 

###### [Service Mesh](./notes/Service%20Mesh.md)

---
Tags: [Programming](Programming.md) - [Systems Design](Systems%20Design.md)

Reference: 
- [Martin Fowler | Microservices](https://martinfowler.com/articles/microservices.html)
- [API Gateway vs Service Mesh vs Message Queue](https://arcentry.com/blog/api-gateway-vs-service-mesh-vs-message-queue/)

Related: 