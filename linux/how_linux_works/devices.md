# Linux Devices

## Device Files

The Kernel on Unix systems present many of the device I/O interfaces to user processes as files known as _device files_ or _device nodes_.

So users can interact with devices like normal files. For instance:

```bash
$ echo blah blah > /dev/null
```

This command will send stdout to a file, but since /dev/null is a device, the kernel bypasses the usual file operations, and uses a device driver on data written to the device. In the case of /dev/null, the kernel accepts the input data, then throws it away.

```bash
$ ls -l
brw-rw---- 1 root disk 8, 1 Sep 6 08:37 sda1
crw-rw-rw- 1 root root 1, 3 Sep 6 08:37 null
prw-r--r-- 1 root root 0 Mar 3 19:17 fdata
srw-rw-rw- 1 root root 0 Dec 18 07:43 log
```

There are 4 types of device files:

### Block Device (b)

Programs can access data from a block device in fixed chunks, for instance a disk device (sda1). Since block device's total size is fixed and easy to index, processes have quick random access to any block in the device with the help of the kernel.

### Character Device (c)

Character devices work with data streams since these devices have no size. The kernel just performs read or write operations on it.

### Pipe Device (p)

_Named pipes_ are like character devices, with another process at the other end of the I/O stream instead of a kernel driver.

### Socket Device (s)

_Sockets_ are interfaces for IPC, often found outside of the /dev directory.

## The sysfs Device Path

The files in /dev give little info about the devices, and furthermore, the kernel assigns devices in the order in which they are found, so a device may hae a different name between reboots.

A uniform view of attached devices based on their actual hardware attributes is offered with the _sysfs_ interface through a system of files and directories. The base path for devices is _/sys/devices_. For example, the SATA hard disk at /dev/sda might have the following path:

```
/sys/devices/pci0000:00/0000:00:17.0/ata3/host0/target0:0:0/0:0:0:0/block/sda
```

So the _/dev_ file enables user processes to use the device, whereas the _/sys/devices_ path is used to view info and manage the device.

## Device Name Summary

The only reliable way to find a device name is querying _udevd_ using **udevadm**

### Hard Disks: /dev/sd\*

Hard disk names start with an _sd_ prefix (e.g., /dev/sda), signifying SCSI disk. _Small Computer System Interface (SCSI)_ was originally developed as a hardware and protocol standard, and the SCSI commmands are still used for USB and SATA devices. The tool _lsscsi_ walks the device paths provided by sysfs.

```
$ lsscsi
[0:0:0:0] disk ATA WDC WD3200AAJS-2 01.0 /dev/sda
[2:0:0:0] disk FLASH Drive UT_USB20 0.00 /dev/sdb
```

- [0:0:0:0] identifies the address of the device on the system
- disk represents its type
- /dev/sda represents device file path
- everything else is vendor info

To combat the randomness of /dev device naming (for instance, sdb could become sda if sda explodes), Linux uses Universally Unique Identifies (UUID) and/or Logical Volume Manager (LVM) stable disk device mapping.

### Virtual Disks: /dev/xvd*, /dev/vd*

Some disk devices are optimixed for virtual machines (e.g., AWS or Virtual Box).

### Non-Volatile Memory Devices: /dev/nvme\*

Some systems use the Non-Volatile Memory Express (NVMe) interface to talk to some kinds of solid-state storage. Use _nvme list_ to view them on the system.

### Device Mapper: /dev/dm-\*, /dev/mapper/\*

A level up from block storage is the LVM, which uses a kernel system called the device mapper. (More in chapter 4)

### PATA Hard Disks: /dev/hd\*

Parallel ATA is an older type of storage bus.

### Terminals: /dev/tty*, /dev/pts/*

_Terminals_ are devices for moving characters between a user process and an I/O device, usually for text output to a terminal screen. They are _pseudoterminal_ devices, emulated terminals that understand the I/O features of real terminals. So, rather than talk to real hardware, the kernel presents the I/O interface to a piece of software (e.g., the shell terminal window).
