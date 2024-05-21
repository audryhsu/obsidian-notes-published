---
creation date: 2022-04-22 17:42
aliases: 
tags: 🖥️
---

**HTTPS** aka secure HTTP (`https://`) encrypts every request/response before being transported over the network to prevent a hacker from *packet sniffing* aka read string messages being sent back and forth (like cookies and `session ids`).

How is HTTPS implemented?
- [Transport Layer Security (TLS)](./Transport%20Layer%20Security%20(TLS).md) cryptographic protocol used for encryption (newer than SSL, secure sockets layer) use certificates to communicate with remote servers and exchange security keys before encryption happens
- most modern browsers check on website's certificate for you

Another way to support security is the **Same-origin policy** which is a concept that permits unrestricted interaction between resources that originate from the same source (aka same URL scheme, hostname, and port). There are legitimate cases where browsers may need access to cross-origin requests (e.g. resources being access via APIs and `fetch`).
- **[Cross-Origin Resource Sharing](./Cross-Origin%20Resource%20Sharing.md)** (CORS) was developed to allow interactions that would normally be restricted to take place by adding new HTTP headers to specify certain origins that servers should serve resources from.
### Session hijacking
Can occur when a hacker obtains a client's unique `session id` and can access the web app posing as the user.

Countermeasures:
- Auth systems will reset sessions upon login, often requiring users to reauthenticate before entering potentially sensitive areas
- Expiration time on sessions
- using HTTPS across entire app

### Cross site scripting (XSS)
User can add raw HTML or JS and submit it to the server, injected into the page contents, and the browser will interpret the code and execute it.

Countermeasures:
- always sanitizing user input, e.g. removing script tags or favoring Markdown format
- escape all user input data when displaying it (e.g. replace HTML characters with ASCII characters so that it displays as plain text)


- [ ] https://developer.mozilla.org/en-US/docs/Learn/Server-side/First_steps/Website_security
- [Express Security Best Practices](https://expressjs.com/en/advanced/best-practice-security.html)
- [Eslint security plugin](https://github.com/nodesecurity/eslint-plugin-security)

---
Tags: [Programming](Programming.md) - [Web Development](Web%20Development.md) - [Cybersecurity](./Cybersecurity.md)

Reference: [Top 10 Security Threats OWASP](https://owasp.org/www-project-top-ten/)

Related: 