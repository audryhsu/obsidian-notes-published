---
creation date: 2022-04-19 11:48
aliases:
  - VM
  - VMs
tags: 🖥️
---

Virtual Machine is a software based computer, referred to as "guest" in contrast to the "host" machine they are run on.
- each VM runs it's own [OS](./notes/Operating%20System.md) and behaves like an independent computer
- hypervisor coordinates between guest VMs and host server to allocate physical resources

Benefits of VM:
- **Efficient resource utilization** -- same as virtualization
- **Scale** - easily deploy multiple copies of same VM to manage increasing workload
- **Portability** - VMs easily can be relocated to other physical servers, and move between on-prem and cloud servers too, great for hybrid.
- **Flexibility** - Creating a VM is faster and easier than installing an OS on physical machine, since it can be cloned with an OS already installed. Agile for testing and development. 
- **Security** - VMs can be rolled back to an older snapshot before infection, or even be deleted and recreated, since it's just software. 


---
Tags: [Cloud](./Cloud.md) - [Infrastructure](Infrastructure.md)

Reference:

Related: 