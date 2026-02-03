+++
author = ["Valdir Junior"]
title = "Securing images using Cosign and Trivy"
date = "2024-02-02"
description = "Learning how to secure your Docker images using Cosign and Trivy"
tags = ["Docker", "Cosign", "Trivy", "Security"]
categories = ["DevOps", "Containers", "Linux"]
+++

![](https://media.licdn.com/dms/image/v2/D4E12AQFgNMFMfdYHnA/article-cover_image-shrink_720_1280/article-cover_image-shrink_720_1280/0/1693557973466?e=1758758400&v=beta&t=7h4EVwvYmt7k-1ArSh7o1IUs3rYhN73Eqeb-BfSyfgU)

Docker image security is a crucial concern for any IT team. Ensuring the authenticity and integrity of Docker images has become an undeniable priority. Docker images are the fundamental building blocks for deploying containerized applications, and any compromise in their security can result in serious consequences, ranging from data breaches to disruptions of critical services.

In this article, we’ll look at how we can use solutions to protect your infrastructure. One of these solutions is Cosign, a Docker image security tool that has become an essential part of any container administrator’s arsenal to ensure image authenticity.

With Cosign, you guarantee that your Docker images are authentic and have not been tampered with.

![Article content](https://media.licdn.com/dms/image/v2/D4E12AQEphSCuFt6o3w/article-inline_image-shrink_1500_2232/article-inline_image-shrink_1500_2232/0/1693560241827?e=1758758400&v=beta&t=kXQDGI9bhucEbQsRqr6y5GfObMJSnqIylp2nKAsvnY4)

Signing Containers – Sigstore

In addition, we will explore how to complement security with Trivy, a vulnerability scanning tool, to build a comprehensive approach to Docker image security.

![Article content](https://media.licdn.com/dms/image/v2/D4E12AQHwGPoxwItGLQ/article-inline_image-shrink_1500_2232/article-inline_image-shrink_1500_2232/0/1693560312187?e=1758758400&v=beta&t=DcMGSYCsHFjTh1w1JpEjAxln7U0h6QN5ANrQsvjH-jI)

Trivy Home - Trivy

---

- **Understanding Docker Image Security**

Docker image security is critical in containerized environments because corrupted or compromised images can have extremely serious consequences, threatening the stability and integrity of entire systems. The threats targeting Docker images are varied and extensive.

1. **Malicious Images:** One of the most common threats is the possibility of malicious Docker images. This occurs when an image is designed to exploit vulnerabilities or open doors for attacks. Such images can be deceptively distributed, often disguised as legitimate or popular resources.

2. **Unpatched Vulnerabilities:** Another major risk lies in unpatched vulnerabilities present in Docker images. An image may contain outdated components or known vulnerabilities that attackers can exploit to gain unauthorized access or compromise system integrity. Lack of regular patch management and updates makes infrastructure vulnerable to these types of threats.

3. **Supply Chain Attacks:** Supply chain attacks have become a growing concern. In these cases, attackers compromise the process of building and distributing legitimate Docker images.

Understanding these threats is essential because Docker image security is not only a technical concern but also a critical matter of data protection and business continuity. This is why tools like **Cosign** and **Trivy** play a crucial role in mitigating these risks.

---

- **What is Cosign?**

Cosign is a digital signing tool that provides authentication and verification for Docker images. It allows you to sign images with security keys, ensuring that only authenticated and trusted images are deployed in your environment.

- **Benefits of Using Cosign**

By adopting Cosign, you can ensure that your Docker images come from trusted sources. This protects against deploying forged or compromised images. Additionally, Cosign provides an extra layer of security, helping to keep your infrastructure safe and reliable.
___

- **Getting Started with Cosign**

To start using Cosign, you can follow these simple steps:

Install Cosign in your environment.

- **MacOS**

```zsh
brew install cosign
```

- **Binary or rpm/dpkg package** ([Cosign release page)](https://github.com/sigstore/cosign/releases/latest)


```zsh
# binary
curl -O -L "https://github.com/sigstore/cosign/releases/latest/download/cosign-linux-amd64"
mv cosign-linux-amd64 /usr/local/bin/cosign
chmod +x /usr/local/bin/cosign

# rpm
LATEST_VERSION=$(curl https://api.github.com/repos/sigstore/cosign/releases/latest | grep tag_name | cut -d : -f2 | tr -d "v\", ")
curl -O -L "https://github.com/sigstore/cosign/releases/latest/download/cosign-${LATEST_VERSION}.x86_64.rpm"
rpm -ivh cosign-${LATEST_VERSION}.x86_64.rpm

# dkpg
LATEST_VERSION=$(curl https://api.github.com/repos/sigstore/cosign/releases/latest | grep tag_name | cut -d : -f2 | tr -d "v\", ")
curl -O -L "https://github.com/sigstore/cosign/releases/latest/download/cosign_${LATEST_VERSION}_amd64.deb"
dpkg -i cosign_${LATEST_VERSION}_amd64.deb
```

Next, generate a key pair. Keep your `.key` file safe as it is extramely important for signing your images.

```zsh
cosign generate-key-pair
```

Finally, sign your Docker images with Cosign using the command below:

- Replace cosign.key with the name of your key
- Replace repository with your repository (for example, on **DockerHub**)
- Replace image and tag with the image and tag that exist in your repository

```zsh
cosign sign --key cosign.key repo/image:tag
```

And to verify a signed Docker image:

```zsh
cosign verify --key cosign.pub repo/image:tag
```

> [!info] The `cosign.pub` file is the public key generated when you created the key pair. It is used to verify the authenticity of the signed image and you can share it with anyone who needs to verify the image.

---

- **_Security Best Practices_**

In addition to using Cosign, it is essential to incorporate vulnerability scanning into your Docker images. As mentioned earlier, a valuable tool for this purpose is **Trivy**. Trivy scans container images for known vulnerabilities and provides detailed information about any potential risks. It can be easily integrated into your CI/CD pipelines to ensure that only secure images are deployed.

To get started with Trivy, you can install it on:

- MacOs

```zsh
brew install trivy
```

- Ubuntu

```zsh
wget https://github.com/aquasecurity/trivy/releases/download/v0.45.0/trivy_0.45.0_Linux-64bit.deb
sudo dpkg -i trivy_0.45.0_Linux-64bit.deb
```

- For make the scan on your images use 

```zsh
trivy image name_of_image:tag
```

The tool will scan the image layers for vulnerabilities and provide a list of any issues found. This allows you to take proactive measures to fix vulnerabilities or choose safer images.

Remember that security is an ongoing process. Scheduling regular scans with Trivy is therefore a recommended practice to ensure that your Docker images remain protected over time.

- Conclusion

In summary, using Cosign and Trivy together can significantly strengthen the security of your Docker images. By adopting these best practices, you can protect your organization against threats, ensuring that your images are authentic and that vulnerabilities are mitigated.
