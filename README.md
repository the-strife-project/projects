# Introduction
This tries to explain the purpose of the repositories in the GitHub organization, so you'll understand what each does quickly.

- The ⚙️ emoji marks repositories that are part of the bootstrapping process of the OS, that is, those that are absolutely necessary, and jotaOS cannot finish boot without. They must be present in all distributions.
- The 🔧 emoji marks a choice of ⚙️. At least one must be present, depending on the purpose of the distro, and multiple of them are fine.
- 📍 marks optional projects. It's fine if they're missing.

# The basics
- ⚙️ [kernel](https://github.com/jotaOS/kernel). If you've come to the point of reading this, you already know.
- ⚙️ [helper](https://github.com/jotaOS/helper). It is a generic Makefile that all projects share, so each individual one is only responsible for setting the respective parameters. For example, the kernel sets `nostdlib := true`, because there's no stdlib available outside of userspace, and the stdlib sets `lib := yes`, because it has to be compiled to a shared object file.
- ⚙️ [stdlib](https://github.com/jotaOS/stdlib). The standard library of all userspace programs. It has mainly two purposes: abstract the interaction with the kernel, and offer commonly used data structures via the STL. For instance, `std::printf` is here.
- ⚙️ [STL](https://github.com/jotaOS/STL). The Standard Template Library, which implements templates (abstract data structures) for programs to use. Examples include `std::vector`, `std::map`, `std::unoredered_map`...

# Drivers
- ⚙️ [term](https://github.com/jotaOS/term). This should actually be 📍, but well. It's the service that writes text to the screen.
- ⚙️ [PCI](https://github.com/jotaOS/PCI). The PCI driver allows communication with PCI and PCI-Express devices, which are present in all modern computers. Most drivers connect to this service in order to look for the devices they support and interact with them.
- 📍 keyboard (TODO). If you want to use the keyboard, you probably need this.
## Block devices
Block devices are pieces of hardware that store data. Examples include a SATA hard disk or a CD-ROM drive.
- 🔧 [AHCI](https://github.com/jotaOS/AHCI). The modern way of connecting to SATA devices: hard disks, SSDs, and CD-ROM drives.
- 🔧 IDE (TODO, might come in the future). A very old way of connecting to ATA (not SATA, but compatible) devices. For this very reason, it's disgusting to work with, so a very simple PIO driver is implemented, which is _very slow_, and prints a warning every time at boot.
- ⚙️ storage (TODO). It provides an abstraction layer over the 🔧s above, by using UUIDs. It's just a way to name all block devices whichever type they are so other services can read and write data to them. Linux used to use `sda`, `sdb`... Windows, `C:`, `D:`...
## Filesystems
Block devices are just bytes. Filesystems organize the bytes in a way so that they can be interpreted as files and directories.
- 🔧 ISO9660 (TODO). It's the filesystem that CDs use. So, if you're booting from an ISO file (burnt CD, cloned USB...), you need this.
- 🔧 RAMFS (TODO). When booting from a read-only environment (such as a CD), it's nice if there's still a way to create files. As the name suggests, all files are stored in RAM, so they're lost on poweroff.
- ⚙️ VFS (TODO). Like with "storage", this is an abstraction level over the above. It provides the concept of mountpoints: one way to represent a directory hierarchy. For example, `/` might be mapped to a RAMFS service, and `/CD` to an ISO9660. Everything inside `/CD/` would be the contents of the CD, and everything outside would be in RAM.
## Network stack

# Programs
- ⚙️ init (TODO). When bootstrapping is complete (VFS is accessible), this is the last program launched by the kernel, and it loads and starts all the others.
- ⚙️ configurator (TODO + name sucks). It is a key-value database (pretty much a JSON) that has the configuration for all programs and drivers. Anyone can access their portion and read it. Its contents are always temporal: an empty database is started at boot. This means that all actual config must be done by other programs (possibly scripts). For instance, firewall configuration goes here. This service also has the permissions for each program: it must be explicitly specified which programs can do what (for example, connecting to "storage" without passing through "VFS").
- 📍 splash (TODO). Just shows the very nice jotaOS logo. It's the very first thing init runs in the official distro.
- 📍 shell (TODO). You know what it is.
- 📍 coreutils (TODO). A project composed of several tools such as `ls`. It would be weird to not have this while having a shell in a distro.
