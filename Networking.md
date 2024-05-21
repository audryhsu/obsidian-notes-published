---
creation date: 2021-12-18 17:50
aliases: 
tags: 🖥️
---

# [Networking](Networking.md)
---
**Internet** is a network of networks.
- computer to computer: a LAN/WLAN (local access network) are several computers tethered together (wired or wirelessly) by a switch to create a network. Switches can connect to routers, which are the gateways in and out of the network.
- network to network: router to router...routers direct traffic.

> [!info]
> Network of networks
>Internet is a network of networks in that the internet is made up of many devices (e.g. computers/servers, wires, routers, switches, etc.) that are interconnected across the world and tethered together by wires or wireless means. Networks can be considered local and as small as two computers at home connected by one switch in a local access network, or larger such as several computer labs at a university connected by a series of switches and routers  The internet is the amalgamation of these  networks of varying size to create one large network, hence, the internet is described as a network of networks. 
>The relationship between protocols at different layers are dependent or linked to each other because protocols at one layer provide a service to the layer above it; this is because data from one layer is encapsulated by the protocol data unit at the layer below. For a general example, application data (like a HTTP request or response) is encapsulated at the Transport layer into a [TCP](./TCP.md) segment (if using TCP). The TCP segment may not care about the content of the HTTP request or response, but it's job is to properly encapsulate the request/response as the segment's data payload and establish a connection with the proper application via its TCP protocols. Likewise, the Internet layer encapsulates a TCP segment and does not concern itself with what port a TCP segment is destined for, as the IP packet's job is to get the TCP segment across the network to it's destination via the IP addresses specified.


**Protocol** is a system of rules that govern the exchange or transmission of data. The various protocols govern different parts of network communication, or address the same part of network communication but do so in different ways for for specific use-cases.

Protocols are for different aspects of communication:
- syntactical rules - structure of a message
- message transfer rules - flow and order of words
	- examples of different protocols for  specific use-cases all relating to message transfer:
		- teacher/student dynamic in a class room (raise hand, get called on, ask question, answer question)
		- friends in a group conversating -- no order needed

Layers of protocol groups for network communication as a metaphor
| Protocol Group                           | Purpose                                                                  |
| ---------------------------------------- | ------------------------------------------------------------------------ |
| Logical Message Structure                | Linguisting rules for how to structrure a message (syntax, word order)   |
| Logical Message Transfer                 | Etiquette for transfer of individual messages (.e.g turn to speak)       |
| Physical message creation/interpretation | Physics and Biology of speech and hearing (e.g. vocal cords and ears)    |
| Physical Message Trasnfer                | transfer of message across the physical medium (e.g. sound waves, wires) |


Two popular models for understanding the protocol layers are OSI Model and Internet Protocol Suite (aka TCP/IP model or DoD model). This is not precise in real-world implementation, but help provide a modularized mental model for how the system works as a whole. 

> [!NOTE]
> Protocols at one layer provide services to the layer above. Data is **encapsulated** into a PDU, creating a separation between protcols operating at different layers. The Transport layer protocol doesn't care what the encapsulated application layer data payload is. Each protocol layer is agnostic of the layer above it. 
>

Using the IPS model and OSI, the layers represent scopes of communications and are listed from top to bottom:
| TCP/IP (OSI)|
| ------------------------------------------------ |
| [Application Layer](./Application%20Layer.md) (Application, Presentation, Session) |
| [Transport Layer](./Transport%20Layer.md)  (Session, Transport)|
| [Internet Network Layer](./Internet%20Network%20Layer.md)  (Network) |
| [Data Link Layer](./Data%20Link%20Layer.md)  (Data link, Physical) |

**Protocol Data Unit (PDU)** is a block of data transferred over a network and consists of a **header**, a **data payload**, and sometimes a **trailer**.
- can be referred to as different names depending on the protocol layers (e.g. frame, packet, segment, datagram..)
- headers and trailers provide protocol-specific metadata about the PDU .e.g Source IP address, destination IP address
- the payload is the key to the way encapsulation (hiding the internal representation of object and preventing it from being accessed from outside the object) is implemented 
	- why? creates separation between protocols at different layers
		- a protocol at one layers doesn't need to know anything about how a protocol at another layer is implemented in order for the protocols to interact
		- can use different protocols at a certain layer without worrying about the layers below
		- e.g. programs with different application protocols (e.g. email, HTTP, FTP) can all use the TCP at the transport layer to transfer their application layer data. Transport layer doesn't care what type of data payload is in the Application layer

Encapsulation: The entire PDU from a protocol at a higher layer is set as the data payload for a protocol at the next layer below.
![Pasted image 20211219105323.png](./images/Pasted%20image%2020211219105323.png)

## Physical Network

- Information is made of bits (binary code has two possible states, 1 or 0);
	- 8 bits = 1 byte
	- 1024 bytes = 1 KB....MB, GB, etc
- We can physically send bits by electricity (ethernet wire), light (fiber optics/copper), radio waves (sent through routers that connect to the physical network)
- **Latency** - time it takes for bits/bytes of data to get from one point in a network to another
	- e.g. mph a car can travel over a fixed distance.
- **Bandwidth** - the maximum capacity of data that can be sent in a particular unit of time (usually seconds) for a device
	- e.g. increasing the number of lanes on a highway increases the bandwidth even if latency stays the same.

### Elements of Latency
⚛️Latency is a limitation of physics and how fast light can travel 

The sum of all the delays that determine overall latency of a network is the total latency.
- **propogation delay**: amount of time it takes for a message to travel from the sender to the receiver, as a ratio between distance and speed.
- **transmission delay**: amount of time taken to navigate an intersection or interchange between different roads (intersections are switches, routers, etc. that connect wires)
- **processing delay**: processing ("checkpoint") that occurs at every interchange
- **queuing delay**: amount of time data is waiting in the queue to be processed (.e.g time spent in the line at the checkpoint) because network devices like routers can only processed fixed amount of data at one time
- **last mile latency**: delays involved in getting the network signal from your ISP's network to your home or office network. A lot of delays take place in parts of the network *closeset to the end points*
	- At the network edge (entry points into a home or corporate LAN), there are more frequent and shorter hops as the data is directed down the network hiearchy to the apporpriate sub-network
	- e.g. you have to drive slower on the back roads getting to the rural mailbox vs the highways

- **round trip time**: latency calculation refers to length of time for a signal to be sent PLUS time for acknowledgement or response to be received

### Bandwidth
varies across a network and won't be consistent across the data transfer journey, because the network devices will be different. A bandwidth bottleneck can occur when bandwidth changes from relatively high to relatively low, and can cause problems when dealing with large amounts of data. 

💸💸💸 You can buy more bandwidth 💸💸💸

> [!NOTE]
> As software engineers, our realm of responsibility in performance improvement is limited to the implementation of the application in how we use higher-level protocols. We can't really do anything about the limitations of the physical network.

---
Tags: [Programming](Programming.md) 

Reference:

Related: 