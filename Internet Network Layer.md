---
creation date: 2021-12-19 17:17
aliases: 
tags: 🖥️
---

# [Internet Network Layer](Internet%20Network%20Layer.md)
---

```ad-tldr

Internet Protocol enables communication between two networked devices anywhere in the world, whereas Ethernet protocol provides communication between devices on the **same** local network.

```
Purpose: facilitate communication between hosts (computers) on different networks (inter-network communication)

The Internet Protocol is the predominate protocol used, with two versions: IPv4 and IPv6. Protocol uses IP addressing and [encapsulation](./Encapsulation.md) of data into packets.
```ad-info
title: Tip

IPv6 uses 128-bit addresses instead of 32-bit addresses to increase number of possible addresses to 340 undecillion.

```

### Packet
**Packet** is the PDU within the IP Protocol and is comprised of a data payload and a header.
- the data payload is the PDU from the above Transport layer (usually a TCP segment or UDP datagram)
- header provides metadata to transfer the packet. Important fields:
	- Version - version of IP used
	- ID, Flags, Fragment Offset - metadata to reassemble a a transport layer PDU if it was sent as multiple packets
	- TTL (time to live): max number of network 'hops' a packet can take before being dropped so they don't endlessly bounce around the network. Routers decrement the TTL value at each hop.
	- Protocol - indicates protocol used for the data payload, e.g. TCP, UDP, etc.
	- Checksum - error checking value. Destination device generates a value using the same algorithm, and if it doesn't match, the packet is dropped. Above layers handle implementation of retransmitting dropped packets.
	- Source address - 32 bit IP address of source/sender of packet
	- Destination Address - 32 bit address of destination of packet

Internet Packet:
![Pasted image 20211222141209.png](./images/Pasted%20image%2020211222141209.png)

### IP address
IP addresses are a critical component to networking because they enable communication between two networked devices are used to identify the source of a packet (source IP address), such as a specific client, and destination of a packet (destination IP address), such as a host server. Each device is designated an IP Address when it joins the network, as IP addresses are logical (not burned-in to a device like MAC addresses).

```ad-note

IP addresses are logical (not tied to physical device) and can be assigned as required to devices as they join a network. Each network and subnetwork has assigned ranges of IP addresses that are available to it to assign devices. This hiearchy means that routers only need to keep track of routers that have access to IP ranges, instead of the exact IP addresses of every single device.

```
This splitting of a network into parts is referred to as sub-netting. By dividing IP address ranges further, subnets can be split into smaller subnets to create even more tiers within the hierarchy.

All routers on a network have a local routing table, which helps it determine where in the network hierarchy the subnet exists and move the IP packet along the best route for it to travel. Routers examine the destination IP Addresses, matches it against a list of network addresses in routing table, and finds which router to send it on to.

Networked *applications* need more than just communicating between devices, since a device can have many applications running. We need the Transport layer protocols to enable communication between specific applications. 
# Example: Journey of Data
Explanation of how MAC Addresses and IP Addresses are used in during the entire journey of data travelling over a network at different layers of communication.

The IP address is responsible for directing the data from one end-point to another end-point. The MAC addresses are responsible for directing the data between each point/ hop of it's entire journey.
```
Client --> LocalRouterA --> Router1 --> Router2 --> Router3 --> LocalRouterB --> Server
```
-   Here we can think of the Client as sending a packet (let's not worry about the layers above the Network layer), to the Server. The Server's IP address is in the packet header.
-   The client encapsulates this packet into an Ethernet Frame, and addresses the Frame to its local router (LocalRouterA) using the MAC address for LocalRouterA (which the Client knows)
-   LocalRouterA receives the Frame, and _decapsulates_ it to access the packet (we don't really cover this in the course, but there's a process of decapsulation and re-encapsulation that occurs on every hop). It reads the IP address of the packet to see where to send it. It then re-encapsulates the packet into a _new_ frame, and addresses the Frame with the appropriate MAC address (if the IP address on the packet is for another device on the local network then it uses the MAC address for that device using a a mapping stored in what is called an ARP table). If the IP address is not for a device on its local network it addresses the Frame to Router1
-   Router1 receives the Frame and follows the same process of decapsulations and re-encapsulation, and addresses the new Frame to Router2 using its MAC address.
-   The same process repeats at Router2 and Router3 (and however many more hops are required -- note also that packets will not always follow the same journey through the network. Routers will direct a packet to the 'next' router based on multiple different criteria).
-   Finally, LocalRouterB receives a Frame from Router3, decapsulates it to read the IP address on the packet, and sees that the address is for a device on its local network. If then re-encapsulates the packet into a new frame for the final time and addresses it to that device using the device's MAC address.

[Video summary of how data moves through the internet](https://www.youtube.com/watch?v=YJGGYKAV4pA)

Tags: [Programming](Programming.md) - [Networking](./Networking.md) 

Reference:

Related: 