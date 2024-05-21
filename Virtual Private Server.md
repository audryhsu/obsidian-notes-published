---
creation date: 2022-04-19 09:41
aliases: 
tags: 🖥️
---

# [Virtual Private Server](Virtual%20Private%20Server.md)
---
A *virtual private server* is a form of multi-tenant cloud hosting in which virtualized server resources are made available via internet from a cloud/hosting provider. VPS can be contrasted to shared hosting and dedicated hosting.

Hosting options
🏨 Shared hosting - all hardware (memory, CPU, storage) and software resources are shared amongst the tenants on a single computer.
- 💲cost effective, especially for applications that don't get a lot of traffic
- Over consumption of resources by one tenant affects other tenants

🏘️ VPS is like a [VM](./Virtual%20Machine.md) but running on a physical server that is shared by other [VMs](./Virtual%20Machine.md) (from other tenants). 
- Share hardware and hypervisor with other tenants on physical server
- Get own [operating system](./notes/Operating%20System.md) to run applications, like a VM
- Gets reserved portion of physical server's resources (memory, compute..)
- 👍 Most scalable, when compared to shared hosting and dedicated hosting
- 💲💲 good balance of cost, control, and "privacy" (isolation from neighbors)

🏡 Dedicated hosting - all hardware and software resources are dedicated to one tenant.
- 👎difficult to scale, since a new physical server would need to be provisioned and configured
- 💲💲💲 most expensive, but most ''private", like renting a single family home
- 👍get to control physical server/geography region that VM is run on
-  *dedicated instance*hosting is another single-tenant option but not tied to a specific physical server.


# Examples
```js

```



---
Tags: [Cloud](./Cloud.md) - [Infrastructure](Infrastructure.md)

Reference:

Related: 