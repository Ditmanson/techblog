---
title: TrueNAS Scale Limitations
date: 2025-06-22
draft: false
description: Failures in TrueNas Scale
tags:
    - k8s
    - k3s
    - nas
    - truenas
---

# Introduction

Navigating the complexities of TrueNAS Scale can be challenging, especially for someone trying to integrate it with Kubernetes. While TrueNAS comes with helpful guardrails designed to prevent mishaps, they end up hindering flexibility and customization—particularly for users who want to run a full-fledged Kubernetes environment.

---

## The Problem: Guardrails vs Flexibility

As someone who's not an expert in TrueNAS, I’ve spent some time exploring its documentation and architecture. I quickly ran into a major limitation: the rigid ecosystem designed to keep users from breaking things. While these "guardrails" are helpful for less technical users, they prevent you from utilizing the full range of features you’d expect in a Linux distribution.

A prime example is my attempt to install **k3s** via a package manager. TrueNAS doesn’t allow the use of traditional package managers like APT or YUM. Instead, it forces users to rely on its UI and App Store, which felt restrictive for what I was trying to achieve. I initially shrugged this off as a minor inconvenience, but it was just the beginning of my frustrations.

---

## A New Issue: Mounting Persistent Volumes

Fast forward to today, I encountered another roadblock while trying to use my TrueNAS system as NFS storage for my **Kubernetes persistent volume claims (PVCs)**. This issue surfaced when I tried to deploy **PostgreSQL** in my cluster. PostgreSQL requires permission to modify its data directory by performing `chmod` or `chown` on the mounted volumes. 

With TrueNAS, you can’t easily configure the NFS server to allow this. Normally, I would edit the `/etc/exports` file and add `no_root_squash`, which would grant the necessary permissions. However, there seems to be no way around this in TrueNAS. This means that I couldn’t mount my PVC from TrueNAS to the PostgreSQL pod, resulting in an error.

---

## The Workaround (or Lack Thereof)

While TrueNAS does offer PostgreSQL as an app, that solution doesn’t fit my needs. I’m looking for a **Kubernetes-native** deployment, where I can configure ingress, egress, and ensure that all my apps are running as pods within the cluster. Running PostgreSQL as a Docker container in TrueNAS isn’t the same as managing it through Kubernetes, where I have more control.

I could set up separate storage for my PostgreSQL pod, but that doesn’t solve the core issue: TrueNAS’s rigid approach doesn’t align with my goal of using Kubernetes for everything.

---

## Conclusion

At the end of the day, TrueNAS is a great system for basic storage management, but when it comes to advanced use cases like Kubernetes, it falls short. If you're looking to run a more flexible, fully managed Kubernetes cluster, you might want to consider alternatives for persistent storage. For me, it’s back to the drawing board for the storage solution. But if you have any suggestions, I’m all ears.

