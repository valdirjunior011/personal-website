+++
author = ["Valdir Junior"]
title = "Docker Networking Basics: Concepts and Examples" 
date = "2024-02-02"
description = " Learning the fundamental concepts of Docker networking, including bridge networks, port forwarding, custom networks, and advanced features like IPvlan, Macvlan, and None Network. Discover how to create and manage Docker networks with practical examples."
tags = ["Docker", "Rede Docker", "Containers", "DevOps"]
categories = ["DevOps", "Containers", "Linux"]
+++

![](https://media.licdn.com/dms/image/v2/D4E12AQFcvRWeihIBbg/article-cover_image-shrink_720_1280/article-cover_image-shrink_720_1280/0/1694442094516?e=1758758400&v=beta&t=FHEiicoOMbEloPH5RMlnSD8_vAUMSe_ILdJP2PUn4yk)

**Docker Networking Fundamentals**

Before diving into more complex topics, it’s important to understand the fundamental concepts of networking in Docker.

- **Bridge Network**

A **bridge network** is the default network created when you install Docker. It allows containers to communicate with each other on the same network using hostnames.

Let’s create a bridge network called `my_network` to demonstrate how it works:

```zsh
docker network create my_network
```

Now, let’s run two containers on this network:

```zsh
docker run --name container1 --network my_network -d nginx
docker run --name container2 --network my_network -d nginx
```

The containers **container1** and **container2** can communicate with each other using the hostnames **container1** and **container2**.

- Port Forwarding

Docker allows you to forward container ports to the host. This is useful when you want to access services inside a container directly from your host system.

For example, to forward port `8080` of the container to port `80` on the host:

```zsh
docker run -d -p 8080:80 nginx
```

> [!tip] Now you can access the Nginx container's content at http://localhost:8080 in your browser.

- **Custom Networks**

Bridge networks are useful, but you can also create custom networks for isolation and greater control. For example, you can create an **overlay network** to enable communication between containers on different hosts.

```zsh
docker network create --driver overlay my_overlay_network
```

> [!warning] You need the following ports open to traffic to and from each Docker host participating in an overlay network:
**TCP** port **2377** for cluster management communications
**TCP** and **UDP** port **7946** for communication among nodes
**UDP** port **4789** for overlay network traffic

![Article content](https://media.licdn.com/dms/image/v2/D4E12AQGvUz-_HmErbQ/article-inline_image-shrink_1500_2232/article-inline_image-shrink_1500_2232/0/1694512271901?e=1758758400&v=beta&t=hd8vawzYXp6TtLjoTTb1D_J1NheS2RYhgLi0p9we4fQ)

https://docs.docker.com/network/drivers/overlay/

---

### Attaching and Detaching Containers

To add containers to an existing network, use the docker network connect command:

```zsh
docker network connect my_network container3
```

To remove a container from a network, use the docker network disconnect command:

```zsh
docker network disconnect my_network container3
```
___

### IPvlan, Macvlan e None Network

In addition to bridge, overlay, and custom networks, Docker offers advanced networking features that can be useful in more complex scenarios. Let’s explore three of these features: **IPvlan**, **Macvlan**, and **None Network**.

- **IPvlan Network**

The IPvlan network allows you to assign individual IP addresses to each container, making them directly accessible on the external network. This is especially useful when you want to avoid Docker’s network encapsulation layer and achieve better performance.

Example of Using **IPvlan**

First, create an IPvlan network:

```zsh
docker network create -d ipvlan --subnet=192.168.1.0/24 --gateway=192.168.1.1 -o parent=eth0 my_ipvlan_network
```
Now, start a container with a specific IP within this network:

```zsh
docker run --name container_ipvlan --network my_ipvlan_network --ip=192.168.1.2 -d nginx
```

The container **container_ipvlan** now has a valid IP address on the external network.

- **Macvlan Network**

The Macvlan network is similar to IPvlan but allows each container to have its own MAC address. This is useful when you need link-layer isolation and a unique MAC address for each container.

Example of Using Macvlan

Create a Macvlan network:

```zsh
docker network create -d macvlan --subnet=192.168.1.0/24 --gateway=192.168.1.1 -o parent=eth0 my_macvlan_network
```

Start a container on the Macvlan network:

```zsh
docker run --name container_macvlan --network my_macvlan_network -d nginx
```

Now, the container **container_macvlan** has its own MAC address and IP on the external network.

- **None Network***

The None network is a network that does not provide external network connectivity for a container—only the loopback interface is created. This can be useful when you want a container’s network completely disabled while it is still running.

Example of Using the None Network

Create a container on the None network:

```zsh
docker run --name container_none --network none -d nginx
```
The container **container_none** will not have external network connectivity but can still be used for internal tasks.

___

### **Network Scopes**

Containers have different levels of network isolation. They can be in a global or local network scope. A bridge network is an example of a local scope, where containers can communicate only on the same host. Overlay networks are global and allow communication between hosts.

### **Cross-Host Communication**

When you have containers on different hosts, you can use overlay networks to connect containers across hosts. To do this, set up a key-value discovery service such as **Consul** or **etcd** so containers can register and discover each other across hosts.

### **Conclusion**

Networking in Docker is a fundamental part of container orchestration and building distributed applications. In this article, we explored concepts such as custom networks, bridge, overlay, IPvlan, Macvlan, and None networks, providing basic examples to illustrate these concepts. Be sure to explore the Docker documentation https://docs.docker.com/network/
 and experiment with the concepts discussed here in your own development environment to gain a full understanding of these topics.
