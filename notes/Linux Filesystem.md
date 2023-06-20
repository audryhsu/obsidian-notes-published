---
publish: true
aliases: 
cssclass: 
---
This is a reference for the mysterious directories that appear under the root directory `/` of a [Linux](../Linux.md#) filesystem.

#### folders under root
`/` - root folder of the host system. Contains binaries, boot files, and other things to run the system.
`/etc` - configuration files for system, shell scripts
`/home` - a folder for every user containing their private data
`/bin` - folder holds binary files, which are executable code for your application or library. 
`/lib` - libraries required by executable programs in the `/bin` folder. Libraries are shared functions between programs. 
`/opt` - optional software that is not essential for system to run
`proc` - virtual files system to run Linux [kernel](./Kernel.md#) files
`/root` - home folder for the root account.
`sbin` - system binaries that are essential. usually used by super user
`srv` - service files sintalled on your system 
`sys`- info and configuration for the system
`usr` - read-only user data
- `usr/bin` programs installed by your linux , non-essential command binaries
- `usr/sbin` - non-essential system binaries, usually run by root
- `usr/share` - shared data used by `usr/bin` like configuration files, icons, themes, wall papers
- `usr/local` - compiled and installed programs by user
`tmp` - temporary files that are cleaned on reboot
`var` - variable files that change all the time, like log files (`var/log`)



---
Tags: 

Reference: https://en.wikipedia.org/wiki/Filesystem_Hierarchy_Standard

Related: 