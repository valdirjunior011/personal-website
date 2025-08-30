+++
author = ["Valdir Junior"]
title = "Dockerfile using Multi-Stage Builds and Secure Images"
date = "2024-02-02"
description = "Otimizing Dockerfile with Multi-Stage Builds and Secure Images using Chainguard"
tags = ["Docker", "Multi-Stage Builds", "Chainguard", "Segurança"]
categories = ["DevOps", "Containers", "Linux"]
+++

![](https://media.licdn.com/dms/image/v2/D4E12AQFiVMmK1xCdPA/article-cover_image-shrink_600_2000/article-cover_image-shrink_600_2000/0/1693653766393?e=1758758400&v=beta&t=Bn7TSy9VPWrDtZ0sT9O2sPazDNEfMBP7Kwz8AT67IKw)

One of the challenges when working with Docker is creating images that are both size-efficient and secure. This article will show an option for optimizing your Dockerfile using Multi-Stage Builds and how the Chainguard tool can be used to ensure Docker image security.

---

- **Optimization with Multi-Stage Builds**

Multi-Stage Builds is an advanced Docker technique that allows you to create smaller, more efficient images. The basic idea is to split the image build into multiple stages, each with its own build environment. This is especially useful when you need to compile source code or perform other complex tasks during the image build process.

- **How Layers Work**

Before diving into Multi-Stage Builds, it’s important to understand how layers work in a Dockerfile. Each instruction in a Dockerfile creates a new layer in the image. Layers are cached, which means that if you don’t change an instruction in the Dockerfile, Docker will reuse the cached layer, saving time and disk space.

However, each layer adds size to the final image. This is important because large images can be problematic in resource-constrained environments and can slow down distribution.

___

- **Multi-Stage Build Example**

A simple example of a Multi-Stage Build for a Node.js application:

```Dockerfile
# Stage 1: Build the application
FROM node:14 as builder
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Stage 2: Create a smaller final image
FROM nginx:alpine
COPY --from=builder /app/build /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

In this example:

1. **Stage 1:** Builds the Node.js application and creates a temporary image with all build dependencies.
2. **Stage 2:** Creates a smaller final image using nginx:alpine and copies only the compiled application output, saving space.

So basically we have two differences:

1. **Without Multi-Stage Builds:** An image including all build dependencies and artifacts can easily grow to several gigabytes, making it heavy and slow to distribute.
2. **With Multi-Stage Builds:** Using intermediate stages results in a much smaller final image containing only what’s necessary to run the application. This can reduce the final image size to tens of megabytes.

___

- **Secure Base Images with Chainguard**

Now that we know how to optimize our Dockerfiles, we should also address Docker image security, this time using Chainguard. Chainguard Images is a powerful extension that enhances image security effectively.

![Article content](https://media.licdn.com/dms/image/v2/D4E12AQEuRTRHiypjig/article-inline_image-shrink_1500_2232/article-inline_image-shrink_1500_2232/0/1693673715207?e=1758758400&v=beta&t=Gmbjzj2PAF4OsnV4QyeHwM6VpCnoxuA4-6RQ8OpJBls)

https://www.chainguard.dev/chainguard-images

- **Chainguard Images**

Chainguard images contain only what is necessary to build or run your application, providing on average an 80% reduction in size and fewer CVEs.

1. **Daily-built images:** Ensures that images are up-to-date and include available security patches.
2. **Signed by Sigstore/Cosign:** Images are cryptographically signed to prove origin and provide tamper-proof guarantees.
3. **SBOM:** Zeroed SBOMs certifying the provenance of all artifacts.

---

- **Using Docker History to View Layers**

As we mentioned earlier about layers, an excellent command to inspect the layers of a Docker image is **docker history**.

How to use it:

```zsh
docker history my-image:latest
```

This will display a list of all the layers that make up the image, along with their IDs, sizes, and corresponding instructions from the Dockerfile.

> [!tldr] The docker history command is useful for understanding how an image was built, which commands were executed in each layer, and the size of each layer. This can help optimize Docker image size and understand how images were created.

### **Conclusion**

Using Multi-Stage Builds in a Dockerfile allows you to create smaller, more efficient images, significantly reducing the final image size. This is crucial for efficient container distribution and deployment. Additionally, using Chainguard images helps ensure Docker image security by verifying the origin and integrity of each layer.

By combining these practices, you can create Docker images that are both more secure and efficient, improving the performance and reliability of your containerized applications.
