# Introduction
This tries to explain the purpose of the repositories in the GitHub organization, so you'll understand what each does quickly.

- The ⚙️ emoji marks repositories that are part of the bootstrapping process of the OS, that is, those that are absolutely necessary, and Strife cannot finish boot without. They must be present in all distributions.
- The 🔧 emoji marks a choice of ⚙️. At least one must be present, depending on the purpose of the distro, and multiple of them are fine.
- 📍 marks optional projects. It's fine if they're missing.
- 🔑 and 📌 are both optional, but in case of using 📌, you need at least one 🔑, like with ⚙️ and 🔧.

# The basics
- ⚙️ [kernel](https://github.com/the-strife-project/kernel). If you've come to the point of reading this, you already know.
- ⚙️ [helper](https://github.com/the-strife-project/helper). It is a generic Makefile that all projects share, so each individual one is only responsible for setting the respective parameters. For example, the kernel sets `nostdlib := true`, because there's no stdlib available outside of userspace, and the stdlib sets `lib := yes`, because it has to be compiled to a shared object file.
- ⚙️ [stdlib](https://github.com/the-strife-project/stdlib). The standard library of all userspace programs. It has mainly two purposes: abstract the interaction with the kernel, and offer commonly used data structures via the STL. For instance, `std::printf` is here.
- ⚙️ [STL](https://github.com/the-strife-project/STL). The Standard Template Library, which implements templates (abstract data structures) for programs to use. Examples include `std::vector`, `std::map`, `std::unoredered_map`...

# Drivers
- ⚙️ [term](https://github.com/the-strife-project/term). This should actually be 📍, but well. It's the service that writes text to the screen.
- ⚙️ [PCI](https://github.com/the-strife-project/PCI). The PCI driver allows communication with PCI and PCI-Express devices, which are present in all modern computers. Most drivers connect to this service in order to look for the devices they support and interact with them.
- 📍 keyboard (TODO). If you want to use the keyboard, you probably need this.
## Storage
### Block devices
Block devices are pieces of hardware that store data. Examples include a SATA hard disk or a CD-ROM drive.
- 🔧 [AHCI](https://github.com/the-strife-project/AHCI). The modern way of connecting to SATA devices: hard disks, SSDs, and CD-ROM drives.
- 🔧 [ramblock](https://github.com/the-strife-project/ramblock). When booting from a read-only environment (such as a CD), it's nice if there's still a way to create files. As the name suggests, this is a block device that behaves like a hard disk, but it's in RAM.
- 🔧 IDE (TODO, might come in the future). A very old way of connecting to ATA (not SATA, but compatible) devices. For this very reason, it's disgusting to work with, so a very simple PIO driver is implemented, which is _very slow_, and prints a warning every time at boot. It's interesting to do because it's what most VMs select by default.
- ⚙️ [block](https://github.com/the-strife-project/block). It provides an abstraction layer over the 🔧s above, by using UUIDs. It's just a way to name all block devices whichever type they are so other services can read and write data to them. Linux used to use `sda`, `sdb`... Windows, `C:`, `D:`...
### Filesystems
Block devices are just bytes. Filesystems organize the bytes in a way so that they can be interpreted as files and directories.
- 🔧 [ISO9660](https://github.com/the-strife-project/ISO9660). It's the filesystem that CDs use. So, if you're booting from an ISO file (burnt CD, cloned USB...), you need this.
- 🔧 [StrifeFS](https://github.com/the-strife-project/StrifeFS). The main filesystem of the OS.
- ⚙️ [VFS](https://github.com/the-strife-project/VFS). Like with "storage", this is an abstraction level over the above. It provides the concept of mountpoints: one way to represent a directory hierarchy. For example, `/` might be mapped to a RAMFS service, and `/CD` to an ISO9660. Everything inside `/CD/` would be the contents of the CD, and everything outside would be in RAM.
## Network stack
All projects here are optional, since a distro could perfectly not need network access. So 🔧 doesn't really mean 🔧, and ⚙️ doesn't really mean ⚙️.
### NICs
- 🔑 RTL8139 (TODO). A driver for the chip of the same name. It's a very simple network chip used in very old Ethernet devices. While uncommon nowadays, most VMs support it and it's just a few click away.
- 🔑 E1000e (TODO, might never come). The network chip used by default on all VMs I know.
- 📌 NIC (TODO). Abstraction over the NIC drivers above (just Ethernet, Wifi is impossible).
### Protocols
- 📍 ARP (TODO). Close to the bare metal, network devices are referrenced by their MAC address. ARP translates an IP to a MAC, so a network packet can be sent.
- 📍 IP (TODO). The base of the Internet. Requires ARP.
- 📍 ICMP (TODO). Mostly for issuing and responding to pings. Requires IP.
- 📍 UDP (TODO). One of the two most commonly used transport layer protocols. Requires IP.
- 📍 TCP (TODO). The other one. Requires IP.

This way, sending a TCP packet follows the route: TCP -> IP -> ARP -> NIC -> RTL8139.
# Programs and services
- ⚙️ init (TODO). When bootstrapping is complete (VFS is accessible), this is the last program launched by the kernel, and it loads and starts all the others.
- ⚙️ configurator (TODO + name sucks). It is a key-value database (pretty much a JSON) that has the configuration for all programs and drivers. Anyone can access their portion and read it. Its contents are always temporal: an empty database is started at boot. This means that all actual config must be done by other programs (possibly scripts). For instance, firewall configuration goes here. This service also has the permissions for each program: it must be explicitly specified which programs can do what (for example, connecting to "storage" without passing through "VFS").
- 📍 splash (TODO). Just shows the very nice Strife logo. It's the very first thing init runs in the official distro.
- 📍 shell (TODO). You know what it is.
- 📍 coreutils (TODO). A project composed of several tools such as `ls`. It would be weird to not have this while having a shell in a distro.
- 📍 DNS (TODO). Allows for resolving domain names (`github.com`). Requires UDP.
- 📍 DHCP (TODO). Even though it's not part of the network stack, it's very much related to it. Asks the router (actually the DHCP server) to assign an IP to you. Requires UDP.
