---
creation date: 2022-03-30 14:56
aliases: 
tags: 🖥️
---

# [Token based Authentication](Token%20based%20Authentication.md)
---
Authentication is important because HTTP protocol is [stateless](./HTTP.md#HTTP%20is%20stateless).  
The traditional method of authentication users was to store session data on an app's server. With rise of modern  apps and mobile apps, server-side authentication did not scale well, because:
1. storing session data for each user in-memory or on-disk increases server overhead
2. rise of cloud and replication of servers to scale for application load (e.g. load balancers) do not work well with session data that is stored in memory on a specific server
3. need to protect session data against cross-site request forgery (CSRF)
4. need for ease of CORS across multiple mobile devices  

After an app validates a user's credentials (username and password), it provides a signed token to the client. The client browser stores the token and sends it with every singe HTTP request for the server to verify before responding with data. 

### Benefits
1. Token based auth is stateless -- user information nor session information is stored anywhere. 
2. No session information means your application can scale and add more machines as necessary without worrying about where a user is logged in.
3. No cookies are being sent back and forth, which helps mitigate CSRF. No session information means you cannot hijack a session. 
4. Tokens are digitally signed with cryptographic means
5. Tokens expire after set amount of time. 

---
Tags: [Programming](Programming.md) - [Backend Development](Backend%20Development.md) 

Reference: [Ins and Outs of Token Based Authentication](https://www.digitalocean.com/community/tutorials/the-ins-and-outs-of-token-based-authentication#toc-how-token-based-works)

Related: 