---
title: Building My First Kubernetes Node
date: 2025-05-26
description: A little about the hardware and general setup as of May.
draft: false
tags:
    - k8s
    - k3s
    - hardware
---

# Introduction

Building a home Kubernetes lab from scratch is no small feat, especially when you’re working with limited resources. I started with the **ZimaBlade**, a compact single-board server, which I initially intended to use as a cheap NAS. But as I dove deeper into Kubernetes, I realized this little machine had potential beyond just storage.

---

## Hardware: The ZimaBlade

The **ZimaBlade** is a single-board server that originally seemed perfect for building a budget NAS. However, after losing an **8TB HDD** to a hardware failure, I decided to pivot and turn this little machine into a Kubernetes node. 

With 16GB of DDR3 RAM, the ZimaBlade is surprisingly capable of running **K3s**, a lightweight Kubernetes distribution. It may not have the power for multiple HDDs, but it’s perfectly suited to my needs as a Kubernetes node.

---

## Operating System: Debian Over CasaOS

The pre-installed **CasaOS** on the ZimaBlade is not great for running Kubernetes. While it's fine for basic Docker apps, it lacks the flexibility needed for Kubernetes management. On top of that, it locks down package managers and certain sudo commands, which was a dealbreaker for me.

I wiped CasaOS and installed **Debian**—the distribution that suits me best for running K3s. The main reason for choosing Debian over alternatives like Ubuntu or Arch was purely aesthetic—Debian just sounded better to me, and it matched my domain name. If I’m being honest, **Ubuntu** might have been a better choice for ease of use, especially if you're just starting out.

---

## Kubernetes Setup: K3s

When it came to choosing a Kubernetes version, I opted for **K3s**. My initial attempts with other solutions like **Docker Desktop** and **Rancher** didn’t work well because I didn’t understand that load balancers are provided by cloud services and don’t work out of the box on bare metal.

I eventually discovered **MetalLB**, which provides load balancing for K3s clusters on bare metal. This discovery changed everything and was a key factor in why I chose K3s for my home lab.

---

## The NAS Setup

My NAS setup is a bit ridiculous—a custom-built case with **RAID 5** (or **RAID-Z1**, according to TrueNAS), **4 HDDs**, and **1TB of PCI SSD**. I initially had grand plans for 12 HDDs and 7 SSDs, but I’ve scaled back to a more reasonable setup.

I’m also running **40GB of DDR4 RAM** and a graphics card that’s mostly going unused. There’s a ton of underutilized resources, which gives me room to expand the cluster in the future. I plan to create a **Debian VM** on the NAS and set up a second node for the Kubernetes cluster.

---

## Getting Started with K3s

Setting up K3s on bare metal wasn’t too hard. The best way to get started is to follow the official [K3s Quick Start Guide](https://docs.k3s.io/quick-start/). One thing I’d note is that some environment variables need to be adjusted, especially when setting up on a home server or custom hardware.

---

## Additional Tools in My Setup

Here are some of the extra tools and technologies I’ve integrated into my home lab:

- **K9s** – For Kubernetes cluster management.
- **Istio** – For managing ingress, including multiple subdomains to one IP.
- **CSI-NFS-Driver** – For managing persistent volumes across the cluster.
- **NGINX** – Two full NGINX deployments for load balancing and reverse proxy.
- **pfSense** – For network management and security.
- **TrueNAS Scale** – For my storage solution (though not without its limitations).
- **Hugo** – For my static site generator.

