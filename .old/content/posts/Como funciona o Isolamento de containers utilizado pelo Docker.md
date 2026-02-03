+++
author = ["Valdir Junior"]
title = "How Docker Uses Linux Kernel Features to Isolate Containers"
date = "2024-01-02"
description = "understanding how Docker uses Linux kernel features like CGroups, Namespaces, and Unshare to isolate containers."
tags = ["Docker", "Linux", "CGroups", "Namespaces", "Unshare"]
categories = ["DevOps", "Containers", "Linux"]
+++

![](https://media.licdn.com/dms/image/v2/D4E12AQHVJusYLjguHA/article-cover_image-shrink_720_1280/article-cover_image-shrink_720_1280/0/1693822466067?e=1758758400&v=beta&t=bPRZ8xzT7u3DhwjV3quxfZMEjyDlriIElSfE-EKA2h0)

Docker has revolutionized the way applications are deployed and managed by using the concept of
containers. One of the fundamental pillars of this feature is isolation, which enables multiple
applications to run on a single host without interfering with one another. This isolation is
achieved through essential Linux kernel features such as Control Groups (CGroups), Namespaces, and
the unshare command. In this article, we will gain an understanding of these features and how they
are used to create isolated environments in Docker.

### Instroduction to Container Isolation in Docker

Isolation is crucial to ensure that containerized applications operate independently in a shared
environment. Without proper isolation, applications could conflict with each other, making
deployment more complex and risky.

To understand isolation in Docker, it is essential to be familiar with three key components:

- **Control Groups (CGroups)**

Control Groups, or CGroups, is a feature that allows controlling and limiting the resources of a
process. Docker uses CGroups to enforce CPU, memory, I/O, and resource limits on containers. This
ensures that an application in one container does not monopolize all system resources, making the
enviroment more balanced and secure.

- **Namespaces**

Namespaces provide isolation of system resources such as process, file systems, and networks. Docker
uses namespaces to create an isolated environment for each container. This means that each container
"sees" only its own processes, file systems, and network interfaces, creating the illusion that it
is running on a separete system.

- **Unshare**

The **Unshare** command allows creating a new namespace for an existing process, enabling proceesses
to run in an isolated environment without the need for Docker or other contatiner managers. Unshare
is a powerful tool for experimentation, testing, and situations where you want to create a custom
container environment.

---

### Running a Container on Linux

Let's now ecplore the steps required to create and run a container on Linux, using essential tools
and concepts such as **CGroups**, **chroot**, **namespaces**, **unshare**, and **debootstrap**.

### Basic Concepts

1. **CGroups (Control Groups)**: Fundamental for resource management, allowing control over CPU,
   memory...
2. **chroot**: Changes the root directory of a process, allowing it to see only a specific
   subdirectory as its root.
3. **Namespaces**: Create isolated instances of system resources such as PID, network, file system,
   and others.
4. **Unshare**: The unshare command is used to create isolated namespaces for a process or group of
   processes.
5. **Debootstrap**: Is a tools that allows the creation of minimal toot file systems for Linux
   distributions. It is commonly used to create initial file systems for containers.

---

### Step 1: Prepare the Root File System

we will use **debootstrap** to create a minimal root file system. In this example, we'll use Ubuntu:

```zsh
sudo mkdir /container
debootstrap focal /container http://archive.ubuntu.com/ubuntu/
```

### Step 2: Configure Resource Isolation

We will use the unshare command to create PID, network, and file system namespaces for the
container:

```zsh
unshare --fork -- pid --mount-proc --uts --ipc --net --map-root-user
```

**What this line does:** The unshare command creates a new process with isolated namespaces in
several areas of the system:

1. **--fork**: Instructs unshare to creaet new child process after isolating the namespaces. The
   child process will run without any specific task right after the namespaces are created.
2. **--pid**: Isolates the PID namespace, meaning the created process will have its own separate
   process space from those outside the PID namespace.
3. **--moun-proc**: Mounts an isolated /proc file system for the created process, giving it its own
   view of /proc.
4. **--uts**: Isolates the UTS namespace, which controls the hostname. This allows the created
   process to have its own hostname.
5. **--ipc**: Isolates the IPC namespace, which manages interprocess communication. this prevents
   the created process form directly interacting with processes outside the IPC namespace.
6. **--net**: Isolates the network namespace, meaning the created process will have its own separate
   network stack and network settings.
7. **--map-root-user**: Allows the created process to be treated as root (superuser) inside the
   namespace, even without root permissions on the host system.

### Step 3: Start the Process Inseide the Container

within the chroot environment, you can start any desired processess. For example:

```zsh
chroot /container /bin/bash
```

### Step 4: Isolate Resource Usage with CGroups

To limit the container's resource usage, you can create a control group (CGroup) for the container's
process and set limits using command like `cgcreate`, `cgset` and `cgexec`.

```zsh
#Install the cgroups package to manipulate these resources
sudo apt install cgroups-tools
```

---

## Examples of Running a Container Using the Concepts Mentioned

### Example 1: Create a Simple Container

Let's create a minimal Ubuntu container and run a shell inside it:

```zsh
# Prepare the root file system
debootstrap focal /container http://archive.ubuntu.com/ubuntu/

# Configure resource isolation
unshare --fork --pid --mount-proc --uts --ipc --net --map-root-user chroot /container /bin/bash

#Start the shell in the container
/bin/bash
```

### Example 2: Isolate CPU Usage with CGroups

Suppose you want to limit the container's CPU usage to 50%:

```zsh
# Create a control group for the container
cgcreate -g cpu:container_group

# Set CPU limit to 50%
cgset -r cpu.cfs_quota_us=50000 container_group

# Run the container process
cgexec -g cpu:container_group /bin/bash
```

---

## Conclusion

This article explored, in a basic and simple way, the fundamentals of creating and running
containers on Linux using tools such as cgroups, chroot, namespaces, unshare, and debootstrap. These
techniques allow processes to be isolated in independent environments, which is the essential
foundation of containerization.

However, it is important to note that tools like Docker greatly simplify the creation and management
of containers. Behind the scenes, these tools combine the mentioned techniques with automation,
image management, networking, and data volumes, providing an easier and more complete experience for
developing and deploying containerized applications. Docker, in particular, uses the set of
technologies mentioned in this article to create and manage containers but also provides a
high-level abstraction layer that makes creating, sharing, and running containers much easier. It
uses an image system, registries, and specific commands to streamline the process. In short, while
understanding these fundamental principles is valuable for

DevOps professionals and sysadmins, the use of tools like Docker has made container adoption much
more accessible and productive for most developers and system administrators.
