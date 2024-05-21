---
creation date: 2021-12-20 13:36
aliases:
  - User Datagram Protocol
tags: 🖥️
---
# [UDP](UDP.md)
---
```ad-tldr

UDP's lack of reliability features are what gives it its strengths: speed and flexibility. A SE creating a UDP-based application can use UDP as a 'base' template and build in parts of reliability services into the application.

```
User Datagram Protocol is used for it's simplicity. The PDU of UDP is a **datagram**, which encapsulates data from the layer above into a payload and adds header information. 
![Pasted image 20211220133808.png](./images/Pasted%20image%2020211220133808.png)

UDP characteristics:
- no guarantee of message delivery
- no guarantee of message delivery order
- no congestion avoidance or flow control
- no connection state tracking (**connectionless protocol**)

Benefits of UDP:
1. Connectionless provides that there is no handshake, so data can be sent immediately without a connection to be established with a receiver. 
2. No acknowledgement and retransmissions means delivery is faster. Datagrams are only sent once, one-directionally, therefore reduced latency.
3. No HOL blocking at transport layer because messages don't need to be sent in order


---
Tags: [Programming](Programming.md) - [Networking](./Networking.md) 

Reference:

Related: [Transport Layer](./Transport%20Layer.md)

Through the use of the Source and Destination Port numbers, UDP provides multiplexing in the same way that [TCP](./TCP.md) does.