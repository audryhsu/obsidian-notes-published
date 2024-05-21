---
creation date: 2022-04-19 10:44
aliases:
  - Proxy
  - proxy
  - proxies
tags: 🖥️
---

# [Proxy Server](Proxy%20Server.md)
---
A proxy server is a "middle man" server that sits in between the client (requesting) machine and the origin server. Proxies act like a gatekeeper to either manage HTTP outgoing requests from client machines (forward proxy) or incoming HTTP requests to origin servers (reverse proxy). 

#### Forward Proxy
A forward proxy (usually referred to as proxy) is a server that sits in front of *client machines*. It intercepts client HTTP requests and communicates with origin servers on behalf o the client. It's like sell-side realtor, that goes out into the market to find deals on behalf of their client based on their requirements. 
![Pasted image 20220419112138.png](./images/Pasted%20image%2020220419112138.png)

**Benefits for client machines:**
1. The client can stay anonymous (IP address in request header is that of the proxy server)
2.  The client may have browsing restrictions (firewall) that the proxy can get around, since the client only needs to connect to the proxy.
3.  Proxy can block access to certain content via content filtering rules by refusing to forward responses back to client group.

#### Reverse Proxy
The proxy server intercepts incoming HTTP requests for a group of origin servers instead of letting clients communicate directly to them. Reverse proxies sit behind the firewall in a private network. Reverse proxies are like buy-side realtor that acts like a middleman to manage offers while keeping buyer anonymous. 
![Pasted image 20220419112150.png](./images/Pasted%20image%2020220419112150.png)

**Benefits for the origin servers:**
1. [Load balancing](./Load%20Balancing.md) - the reverse proxy server can distribute incoming traffic evenly among the origin servers to prevent one from being overloaded (single point of failure)
2. Protects from DDoS attacks - Proxy also hides the IP address of the origin server, so they never get attacked directly. The reverse proxy IP will be visible, however if using a vendor proxy, these usually have more security and resources to find off a cyber attack.
3. Global [load balancing](./Load%20Balancing.md) - reverse proxy can send clients to a origin server that is geographically closest to them to reduce latency. 
4. Caching - reverse proxy can also cache content at network edge to reduce latency
5. Streamlined SSL encryption - The reverse proxy can be decrypt all incoming requests and SSL encrypt all out going resources for the origin server.


##### Reverse proxies are more than just load balancers
While load balancers are needed to handle spikes in traffic, a reverse proxy has value even if there is only one origin server because of the i*ncreased security* by keeping origin server anonymous. This layer of abstraction also ==allows you to change the configuration of your backend architecture== (e.g. scale servers up or down) without any clients needing to know or care  

---
Tags: [Cloud](./Cloud.md) - [Infrastructure](Infrastructure.md)

Reference:

Related: 