+++
author = ["Valdir Junior"]
title = "Docker Distroless: Healthchecks and Auto-Restart"
date = "2024-02-02"
description = "uncovering the power of Distroless images in Docker, focusing on security and monitoring with healthchecks and auto-restart capabilities."
tags = ["Docker", "Distroless", "Healthchecks", "Auto-Restart"]
categories = ["DevOps", "Containers", "Linux"]
+++

![](https://media.licdn.com/dms/image/v2/D4E12AQGwhxdbDGnDjA/article-cover_image-shrink_720_1280/article-cover_image-shrink_720_1280/0/1696490979793?e=1758758400&v=beta&t=ImtknOIDkgq-8dIyXQIsdUdtaiwKKM6Cty88-4Ina9M)

## Simplifying Container Image Efficiency

### Distroless: What They Are and Why They Matter

Distroless images are a special class of Docker images that stand out for their minimalist approach.

Unlike traditional images that include a full operating system, distroless images contain only what
is necessary to run an application. This means there are no unnecessary packages or utilities, which
significantly reduces the container’s attack surface.

The importance of distroless images is twofold. First, they reduce the amount of potentially
vulnerable software present in the container. Second, they make containers lighter and faster to
deploy, saving hardware resources and improving scalability.

To inspect the layers of a distroless image and ensure its provenance, you can use the `dive` tool.

[Dive - Analyze Docker Images (](https://gochronicles.com/dive/)[gochronicles.com](http://gochronicles.com/)[)](https://gochronicles.com/dive/)

It allow you to inspect an image's layers and check for any unwanted content.

For example:

```zsh
dive gcr.io/distroless/base-debian10
```

---

### Healthchecks: Keeping the Container Healthy

Now that we know how to make our containers more secure with distroless images, it’s time to add
another layer of robustness: `healthchecks`.

A healthcheck is a function that verifies whether the container is in a healthy and functional
state. This is essential for monitoring and ensuring the continuous availability of your
application.

Healthchecks can be implemented directly in the Dockerfile. Here’s an example:

```Dockerfile
# Use a distroless base image
FROM gcr.io/distroless/base-debian10

# Copy application files into the container
COPY my_app /my_app

# Define the port your application listens on
EXPOSE 8080

# Add a healthcheck
HEALTHCHECK --interval=5s --timeout=3s \
  CMD curl -f http://localhost:8080/health || exit 1

# Command to start your application
CMD ["/my_app"]
```

In this example, we use the HEALTHCHECK instruction to define a health check that runs the curl
command to verify whether our application’s /health endpoint is responding correctly. If the health
check fails, the container will be marked as unhealthy and can be restarted automatically.

---

### Automatic Restart with Healthchecks

One of the benefits of healthchecks is automatic restart. When you use container orchestrators like
Kubernetes, Docker Compose, or Swarm, they can monitor healthcheck statuses and take automatic
action when a container becomes unhealthy.

For example, in Kubernetes, you can specify the restart policy for your pods:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: my-app
spec:
  containers:
    - name: my-app-container
      image: minha-imagem:tag
      ports:
        - containerPort: 8080
      livenessProbe:
        httpGet:
          path: /health
          port: 8080
        initialDelaySeconds: 5
        periodSeconds: 5
```

In this example, Kubernetes monitors the container’s /health endpoint and automatically restarts the
pod if the health check fails. This ensures your application is always running and healthy.

Refer. Documentação Kubernetes.

![Article content](https://media.licdn.com/dms/image/v2/D4E12AQEya34_r8_tAQ/article-inline_image-shrink_1500_2232/article-inline_image-shrink_1500_2232/0/1696491718381?e=1758758400&v=beta&t=K4LJm_aPtT5kovSuXgS9yAZuYR1H-UURoVtD46GcQd0)

Configure Liveness, Readiness and Startup Probes | Kubernetes

---

### Conclusion

Security and monitoring are crucial elements for the success of containers in production
environments.

Using distroless images, implementing healthchecks, and properly customizing your Dockerfile or
Kubernetes YAML are best practices to achieve these goals.

By minimizing the attack surface, maintaining control over container health, and leveraging
automatic restarts, you take important steps toward a more secure and reliable container
architecture.

Always remember to adapt these practices to the specific needs of your application and environment.
