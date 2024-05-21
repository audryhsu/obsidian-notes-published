---
creation date: 2021-12-20 10:45
aliases: 
tags: 🖥️
---

# [Transport Layer](Transport%20Layer.md)
---
Important concepts to master:
- how transport layer protocols enable communication between processes (multiplexing and demultiplexing)
- network reliability is engineered
- Understanding tradeoffs between [TCP](./TCP.md) and UDP

## End to end application communication
We can think of the different applications running on a host as distinct *channels* for communication on a host machine. The destination IP address from the IP packet get us to the right host, now we need another way to route the packet to the right application channel. Enter **ports**.

🧠 Mental model
- IP address is the apartment building address 🏨
- port number is the apartment unit number. 🔢
- A port is the mail slot in the door to get access inside 📬
- UPS is the Internet Protocol 📨 that gets the message to the building
- The building concierge 🏃‍♂️ is the Transport Layer protocol ([TCP](./TCP.md) or UPD) that gets it to the right port 
- We need both to get the message to the right place.

```ad-tldr

The IP address and the port number _together_ are what enables end-to-end communication between specific applications on different machines.

```

![Pasted image 20211220110415.png](./images/Pasted%20image%2020211220110415.png)

**Multiplexing** is the concept of transmitting multiple signals over a single channel. 
- This can apply to multiple contexts, such as Transport layer our network communications or at the physical level when fiber optic cable can carry multiple light signals at different angles of refraction
- Multiplexing is enabled through the use of *network ports*
**Port** is an identifier for a specific process running on a host, with an integer range between 0 - 65535, with ranges reserved for specific purposes:
	- well -known ports for HTTP, FTP, SMTP, etc.
	- registered ports assigned to private entities to provide specific services (e.g. Microsoft, IBM)
	- dynamic, or private, ports: cannot be registered, they are customized or allocated as *ephemeral* ports

An application running a client machine will have an ephemeral (temporary) port assigned to it by the [operating system](./notes/Operating%20System.md). 

### Sockets and Connections
**Socket** is a communications connection point (endpoint), or a combination of IP address and port number. Sockets allow information exchange between processes on the same machine or across a network. Socket objects (in code implementation) are used to create connections between applications.

```ad-tldr

We can create more reliable connections with connection-oriented system because this dedicated connection allows us to establish more rules in place for order of messages, retransmission of dropped packets, and acknowledgements.


```

There are two ways of managing network communications on a host: *connectionless* and *connection-oriented*.
- Connectionless is where you have one socket defined by the IP address of the host machine 'listening' on port 53 that receives and responds to all incoming messages directed to the particular IP/port pair. These messages can come from any source, at any time, in any order.
- Connection-oriented system is where a new, socket object dedicated to the IP and port number of the process/host which sent the message is instantiated, designed to listen specifically for all messages for that particular process/host, or four-tuple (source IP/port and destination IP/port all match). 
	- *creates a dedicated virtual connection* for communication between a specific process running on one host and a specific process running on another host.
	- any messages not matching the four-tuple is picked up by the original socket (e.g. the "generalist" socket listening on the port), which would then instantiate another socket object to establish new connections for new processes.

```ad-summary
title: Connection Oriented Communication

Connection-oriented service is used to create a **dedicated, end-to-end virtual connection** for a specific process between sender and receiver before transmitting data over the network. Mental model is a telephone operator establishing a connection before the conversation begins.

[[TCP]] is a connection-oriented protocol. UDP is connectionless.

```

Illustrative example of connection-oriented service where multiple sockets are created for different connections on port 80.
![Pasted image 20211222190439.png](./images/Pasted%20image%2020211222190439.png)

### Network Reliability
The lower layers of network communication are *unreliable* channels, because if a ethernet frame or an Internet Protocol packet are discarded due to corruption, the protocols don't have a provision to enable replacing the lost data. They leave it up to the Transport layer protocol to implement a reliable communication channel . With more reliability, we tradeoff performance.

Building reliability features involves:
- in-order delivery of messages (numbering our messages),
- data integrity via error checking (checksum for corrupt messages),
- retransmission of lost data (triggered by lack of ack), and
- deduplication handling (numbering our messages)

[Fullstack Open Project (FSO)](./Fullstack%20Open%20Project%20(FSO).md)


Tags: [Programming](Programming.md) - [Networking](./Networking.md) 

Reference:

Related: [UDP](./UDP.md)