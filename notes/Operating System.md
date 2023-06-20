---
creation date: 2022-04-19 11:53
aliases: [OS]
tags: 🖥️
publish: true
---
Tags: [Computers](../Computers.md#) 

Operating system is a system software that provides a set of services and utilities to manage the computer's resources and enable user applications to run. The [Kernel](./Kernel.md#) is the heart of the OS.

It is executed by the bootstrap program when a [computer](../Computers.md#) is first booted up. 
![Drawing_2023-06-19 computer layers diagram.excalidraw.svg](../images/Drawing_2023-06-19%20computer%20layers%20diagram.excalidraw.svg#)
OS is made up of other, higher-level components :
- device drivers - interface that enable interaction between OS and hardware devices e.g. disk drives, network adapters, graphics cards, i/o devices
- [filesystem](../Linux%20Filesystem.md#) - manages how files and directories are organized, stored, and accessed (e.g. file permissions, metadata, functionality for creating/reading/writing/deleting files)
- [process](../Processes%20vs%20Threads.md#) management - creating, scheduling, and terminating user-level and system processes
	- managing concurrent processes, allocates CPU time, and provides mechanisms for inter-process communication & synchronization
- [Memory](Memory.md#) management - allocation, deallocation, and efficient utilization of memory resources
- User interface - CLI  & GUI
- Security & access control components to protect OS and it's resources
	- auth, encryption, firewalls, etc.
- System utilities - tools and apps that come with the OS for system admin and performance monitoring
	- text editor, command-line utilities, disk partitioning tools, performance monitoring tools
 - Networking & communication - manages network connections, protocols, and configs for routing and security (e.g. IP addressing)