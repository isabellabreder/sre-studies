---
attachments: [os-structure.png, vms.png]
tags: [CLI, kernel, linux, operating system, os]
title: Linux notes
created: '2024-10-15T21:23:00.816Z'
modified: '2024-10-21T14:00:01.636Z'
---

# Linux notes
------------------------
* [Operating Systems](#operating-systems)
* [Virtualization](#virtualization)
* [Linux File System](#linux-file-system)
------------------------
<h2 id="operating-systems">Operating systems</h2>

Operating system is the layer between the hardware and the applications; if there wasn't that layer, all of the user apps would have to handle hardware resources themselves

```mermaid
graph TD
    A[Applications] --> B[Operating System]
    B --> C[Kernel]
    C --> D[Hardware]
    B -->|Manages| E[Resource Management]
    B -->|Manages| F[Process Management]
    B -->|Manages| G[Memory Management]
    B -->|Manages| H[File System Management]
    B -->|Manages| I[Device Management]
    E --> C
    F --> C
    G --> C
    H --> C
    I --> C
```

- **Main Responsibilities**:
  - **Resource Management**:
    - Manages CPU, memory, storage, and I/O devices
    - Allocates resources to various applications and users, balancing performance
  - **Process Management**:
    - Handles the creation, scheduling, and termination of processes
    - Manages process states: running, waiting, and ready
  
  ```mermaid
  graph TD
    A[New] -->|Process Created| B[Ready]
    B -->|Scheduler Picks| C[Running]
    C -->|I/O Request| D[Waiting]
    D -->|I/O Completed| B
    C -->|Time Slice Expired| B
    C -->|Process Completed| E[Terminated]
  ```

  - **Memory Management**:
    - Controls memory allocation to processes
    - Uses concepts like **virtual memory** to extend physical memory

  ```mermaid
  graph TD
    A[Virtual Memory] --> B[Page Table]
    A2[Process 1] -->|Uses| A
    A3[Process 2] -->|Uses| A
    A4[Process 3] -->|Uses| A
    B --> C[Physical Memory]
    C --> D[Paging System]
    D -->|Maps Virtual to Physical| A
  ```

  - **File System Management**:
    - Manages file storage, organization, and access
    - Uses directories, permissions, and file types to maintain structure
  
  ```mermaid
  graph TD
    A[Root Directory]
    A --> B[Home]
    A --> C[Etc]
    A --> D[Var]
    A --> E[Usr]

    B --> F[User1]
    B --> G[User2]

    F --> H[Documents]
    F --> I[Downloads]
    F --> J[Pictures]

    C --> K[Config1.conf]
    C --> L[Config2.conf]

    D --> M[Log]
    D --> N[Cache]

    E --> O[Bin]
    E --> P[Lib]
    E --> Q[Share]
  ```

  - **Device Management**:
    - Manages input/output devices (e.g., keyboards, monitors)
    - Uses device drivers to communicate with hardware

- **Types of Operating Systems**:
  - **Single-User vs. Multi-User**:
    - Single-user OS: One user at a time (e.g., Windows Home)
    - Multi-user OS: Supports multiple users simultaneously (e.g., Unix)
  - **Batch OS**:
    - Executes a series of jobs without manual intervention
    - Common in early mainframe computers
  - **Real-Time OS (RTOS)**:
    - Designed for applications that need immediate processing (e.g., medical systems)
  - **Distributed OS**:
    - Manages a group of distinct computers, making them appear as a single system
  - **Embedded OS**:
    - Optimized for embedded devices like smart appliances, with minimal resources

- **Kernel**:
  - Core component of an OS that manages system resources
  - Handles communication between hardware and software
  - **Types of Kernels**:
    - **Monolithic Kernel**: All OS services run in a single space (e.g., Linux)
    - **Microkernel**: Minimal core services, with additional services running separately (e.g., Minix)

<p align="center">
  <a href="https://github.com/isabellabreder/sre-studies/blob/linux/00-linux/attachments/os-structure.png">
    <img src="https://github.com/isabellabreder/sre-studies/blob/linux/00-linux/attachments/os-structure.png?raw=true" width="800" alt="Editor" />
  </a>
</p>


------------------------
<h2 id="virtualization">Virtualization</h2>

- **Virtual Machines (VMs)**
  - A VM is an instance of an operating system running in isolation within another operating system
  - It allows different operating systems to run on the same physical hardware
  - Each VM has its own OS and shares the physical resources (CPU, memory, storage) of the host
  - Provides full isolation, where each VM operates like a separate physical computer (the computer doesn't know that it's placed on top of another one)
  - If something breaks inside the VM, it doesn't affects the actual computer

<p align="center">
  <a href="https://github.com/isabellabreder/sre-studies/blob/linux/00-linux/attachments/vms.png">
    <img src="https://github.com/isabellabreder/sre-studies/blob/linux/00-linux/attachments/vms.png?raw=true" width="800" alt="Editor" />
  </a>
</p>

- **Hypervisor**
  - The hypervisor (or virtual machine monitor) is the software layer that enables multiple virtual machines to run on a single physical machine
  - It manages the distribution of hardware resources among VMs
  - It creates a virtual environment that acts like hardware for each VM
  - They are essential for creating and managing VMs

- **Types of Hypervisors**
  - Type 1 (Bare-metal Hypervisor):
    - Runs directly on the host's hardware, with no underlying operating system
    - Examples: VMware ESXi, Microsoft Hyper-V, and Xen
    - Generally more efficient because it doesn't require a host OS
    - Suitable for enterprise environments
  - Type 2 (Hosted Hypervisor):
    - Runs on top of a host operating system.
    - Examples: VMware Workstation, Oracle VirtualBox, and Parallels Desktop
    - Easier to set up and use, making it ideal for development and testing environments

<p align="center">
  <a href="https://github.com/isabellabreder/sre-studies/blob/linux/00-linux/attachments/hypervisor.png">
    <img src="https://github.com/isabellabreder/sre-studies/blob/linux/00-linux/attachments/hypervisor.png?raw=true" width="800" alt="Editor" />
  </a>
</p>

- **Benefits and usage**
  - **Type 1**:
    - Efficient usage of hardware resources
    - Use all the resources of a performant big server
    - Users can choose any resource combination
    - Abstraction of the OS from the hardware
    - Virtual machine image (VMI): the OS as a portable file
  - **Type 2**:
    - You don't have to buy a new computer for learn and experiment
    - Don't endanger you main OS
    - Testing some app on different OS

- **Key concepts**
  - **Virtualization host**: the physical machine that hosts the VMs 
  - **Guest OS**: the OS running inside a VM
  - **Virtual network**: hypervisors can create virtual networks that allow VMs to communicate with each other or with external networks
  - **Snapshots**: saved state of a VM at a specifc point in time, allowing rollback 
  - **VM migration**: process of moving a VM from one host to another with minimal downtime, useful for load balancing and maintenance


------------------------
<h2 id="linux-file-system">Linux File System</h2>


- **Hierarchical Tree Structure**: 
  - The file system is organized in a hierarchical tree with directories and subdirectories, starting from a single root directory (`/`).

- **1 Root Directory (`/`)**:
  - The top-most directory in the Linux file system.

<p align="center">
  <a href="https://github.com/isabellabreder/sre-studies/blob/linux/00-linux/attachments/filesys.png">
    <img src="https://github.com/isabellabreder/sre-studies/blob/linux/00-linux/attachments/filesys.png?raw=true" width="800" alt="Editor" />
  </a>
</p>

- **`/home`**:
  - Home space for all non-root users.
  - Allows multiple users on the same computer, with each having their own space and configurations.
  - Contains hidden files (dotfiles), usually starting with a dot (`.`), which are often configuration files automatically generated by programs or the OS.

- **`/root`**:
  - Home directory for the root (superuser).
  - Separate from `/home` to provide isolation between regular users and the root user.

- **`/bin`**:
  - Contains essential user command binaries (executables), like `ls`, `cp`, `mv`, etc.
  - Available to all users and required for basic system operations.

- **`/sbin`**:
  - Contains system binaries that require superuser privileges to run.
  - Includes commands for system administration, like `fdisk`, `reboot`, etc.

- **`/lib`**:
  - Holds shared libraries required by the binaries in `/bin` and `/sbin`.
  - Libraries are similar to DLL files in Windows.

- **`/usr`**:
  - Originally intended for user binaries; now contains secondary, non-essential applications and libraries.
  - Mirrors the structure of the root directory for historical reasons.
  - **`/usr/local`**: 
    - Contains software installed manually by the user.
    - Programs here are available to all users. To make software available only to a specific user, install it in `/home`.

- **`/opt`**:
  - Directory for optional, third-party software.
  - Used for applications that don’t follow the usual Linux directory structure.
  - Unlike `/usr/local`, software installed here often keeps all its files in a single subdirectory.

- **`/boot`**:
  - Contains files needed for booting the system, including the Linux kernel, boot loader configuration, and initial RAM disk.

- **`/etc`**:
  - Contains configuration files for system-wide applications.
  - Writable and used for storing configuration settings.

- **`/dev`**:
  - Holds device files that represent hardware devices (e.g., webcam, mouse, disk drives).
  - Allows applications and drivers to interact with the hardware.

- **`/var`**:
  - Stands for "variable."
  - Contains files that change frequently, like logs, spool files, and caches.
  - Examples include `/var/log` for log files and `/var/cache` for cached data.

- **`/tmp`**:
  - Temporary directory for files required by the system or applications for short-term use.
  - Files in `/tmp` are typically deleted on system reboot.

- **`/media`**:
  - Contains mount points for removable media like USB drives, CDs, and DVDs.
  - When a device is inserted, it is automatically mounted here.

- **`/mnt`**:
  - Historically used for manually mounting file systems or external drives.
  - Often used by system administrators for temporary file systems.

