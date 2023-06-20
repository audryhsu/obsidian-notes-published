---
tags:
aliases: 
publish: true
---
References: [Operating System](./Operating%20System.md#)

---
The kernel is the heart of an [Operating System](./Operating%20System.md#) and is responsible for more low-level processes, primarily in *bridging communication between the hardware and software layers*. It is a software process that is loaded into memory when the OS is started upon computer boot up.

![Drawing operating system.svg](../images/Drawing%20operating%20system.svg#)


The kernel runs in the background (like a daemon) and responds to [System call](../System%20call.md#)s incoming from user-level processes. 

Main responsibilities:
- hardware interaction --> interacts directly with processor, memory, and devices
	- manages hardware resources, handles interrupts, and device drivers
- core system services
	- manages system resources
	- memory management
	- process scheduling
- privileged operations - kernel operates in privileged mode to directly access system resources that user-level processes cannot

# kernel space vs user space
**Kernel space**
- kernel code is usually loaded into a separate area of memory (kernel space) to run privileged operations and device drivers
	- protected from access by [application software](https://en.wikipedia.org/wiki/Application_software "Application software") 
- separation protects memory and hardware from malicious or malfunctioning applications 
 
**User space** 
- application programs such as browsers, word processors, or audio or video players use a separate area of memory, aka [user space](https://en.wikipedia.org/wiki/User_space_and_kernel_space "User space and kernel space"). 