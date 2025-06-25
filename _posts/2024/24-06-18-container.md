---
title: Container system
mathjax: true
toc: true
categories:
  - Study
tags:
  - container
---

Summary from this [post](https://vineetcic.medium.com/the-differences-between-docker-containerd-cri-o-and-runc-a93ae4c9fdac) 

## 1 OCR vs CRI 
- OCI (Open Container Initiative): a set of standards for containers, describing the image format, runtime, and distribution.
- CRI (Container Runtime Interface) in **Kubernetes**: An API that allows you to use different container runtimes in Kubernetes.
![Alt text](/code23/assets/images/2024/24-06-18-container_files/overview.webp)

## 2 Lifecycle of docker
when you run a container with docker, you’re actually running it through the **Docker daemon**, which calls **containerd**, which then uses **runc**.
![Alt text](/code23/assets/images/2024/24-06-18-container_files/lifecycle.webp)
- Docker daemon: dockerd
- containerd: High-level container runtime
- runc: Low-level container runtime. (Includes libcontainer, a GO lib for creating containers)

## 3 K8s 
- K8s used to use Docker Engine to run containers
- K8s created CRI to run any containers
- Then it uses **dockershim** to run Docker container
- dockershim was removed completely, use containerd as successor to Docker Engine.

CRI is an interface used by Kubernetes to control the different runtimes that create and manage containers.
![Alt text](/code23/assets/images/2024/24-06-18-container_files/k8s.webp)

Red Hat’s OpenShift uses CRI-O

## Docker vs Podman
Podman is an open-source container orchestrator under the OCI standards developed by Red Hat.

It's the default container engine in RedHat 8 and CentOS 8.

- Docker uses a daemon to create images and run containers. 
- Podman has a daemon-less architecture

*Rancher* Enterprise k8s mangement