+++ 
author = ["Valdir Junior"] 
title = "Docker Volumes: Share and Persist Data Efficiently" 
date = "2024-03-02" 
description = "How to use Docker volumes to share and persist data efficiently in your applications." 
tags = ["docker", "volumes", "containers", "devops"] 
categories = ["DevOps","Containers", "Linux"] 
+++

![](https://media.licdn.com/dms/image/v2/D4E12AQEerXYc8JGnFA/article-cover_image-shrink_720_1280/article-cover_image-shrink_720_1280/0/1694435515394?e=1758758400&v=beta&t=UxGuBTGGWEnDDex9-bjyIXHAQMHQx4tLUMqzjdeY4Us)

One of the fundamental features that make Docker a popular choice is the ability to persist data
across container runs. This is possible thanks to Docker volumes. In this article, we’ll explore the
concept of Docker volumes comprehensively.

---

**What Are Docker Volumes?**

Simply put, Docker volumes are mechanisms that allow data to be shared and persisted between
containers and the host where Docker is running. They are especially useful for storing data such as
databases, logs, or any other information that needs to "survive" beyond a container’s lifecycle.

Docker volumes offer several advantages, including:

1. **Data Persistence**: Data stored in volumes is not lost when a container stops or is removed.

2. **Data Sharing**: Volumes can be shared across multiple containers, enabling them to access the
   same data.

3. **Improved Performance**: Compared to copying data directly in and out of containers, using
   volumes can significantly improve I/O performance.

4. **Simple Backup and Restore**: Since data is isolated in volumes, backing up and restoring
   becomes easier.

---

Now that we understand why volumes matter, let’s look at how to use them.

## Using Docker Volumes

### Mount Types

- **Host Volume Mounts (Bind Mounts)**: Host volumes, or bind mounts, allow you to mount directories
  or files from the host’s filesystem directly into a container. This means you can share specific
  host data with a container. To create a bind mount, use the -v or --volume option followed by the
  host path and container path.

```zsh
docker run -v /path/on/host:/path/in/container my_app
```

> This mounts the /path/on/host directory from the host into the container at /path/in/container.

- **Docker Volumes (Managed by Docker)**: Docker volumes are Docker-specific volumes managed by the
  Docker daemon. They are created automatically and are generally more flexible regarding where and
  how data is stored on the host. To create a Docker volume, you can run:

```zsh
docker volume create my_volume
```

This creates a volume named my_volume that can be mounted into one or more containers.

---

### Mounting a Volume in a Container

Now, let’s create a container and mount our volume to it. Suppose you have an app that needs to
store data in a volume.

```zsh
# -d to run in detached mode (background)
docker run -d -v my_volume:/app/data my_app
```

This command creates a new container from the image my_app and mounts the volume my_volume to
/app/data inside the container. Any data written to /app/data inside the container will be stored in
the my_volume volume on the host.

**Sharing Volumes Between Containers**

One of the benefits of volumes is the ability to share them between containers. This is useful when
you want multiple containers to access the same data. To do this, simply mount the same volume in
different containers.

```zsh
docker run -d -v my_volume:/app/data container_1
docker run -d -v my_volume:/app/data container_2
```

Now, both `container_1` and `container_2` can access and modify the same data stored in the
my_volume volume.

> Since we don’t specify permissions, by default all containers have read-write (rw) access.

**Viewing Volumes**

To list all Docker volumes on your system, use:

```zsh
docker volume ls
```

To inspect a specific volume, use:

```zsh
docker volume inspect my_volume
```

---

### Examples of Using Docker Volumes

**Example 1: Storing Data in a Database**

Let’s run a MySQL database container and create a volume to store its data:

```zsh
docker volume create database_data
docker run -d -v database_data:/var/lib/mysql mysql:latest
```

This ensures the database data persists in the database_data volume.

**Example 2: Sharing Logs**

Suppose you have a web app and a log server running in separate containers. You can create a volume
to share logs between them:

```zsh
docker volume create logs
docker run -d -v logs:/app/logs my_web_app
docker run -d -v logs:/app/logs log_server
```

Now both containers can access the same logs in the logs volume.

---

### Conclusion

Docker volumes are an essential tool for managing data efficiently and persistently in containers.
They allow you to persist data, share information between containers, and improve I/O performance.
With the concepts and examples presented here, you’re ready to start using Docker volumes in your
own projects. Explore further and experiment with different use cases to sharpen your Docker skills.

Official documentation:
[https://docs.docker.com/storage/volumes/](https://docs.docker.com/storage/volumes/)
