---
title: "ArgoCD"
date: 2025-06-15
description: "Today we bring up ArgoCD"
draft: false
tags:
  - k8s
  - argocd
---

# Bringing Up ArgoCD

## Day One with Argo

Today I finally got ArgoCD up and running on my home cluster. I'm mostly following the [official docs](https://argo-cd.readthedocs.io/en/stable/operator-manual/declarative-setup/), but as with most things in self-hosted Kubernetes, there were a few snags worth documenting.

---

## Problem 1: Ingress Conflicts with Istio

The first thing I noticed was that my current setup didn’t play well with Argo’s recommended [Istio ingress configuration](https://argo-cd.readthedocs.io/en/stable/operator-manual/ingress/#istio). I’m not using Kustomize, and I run MetalLB instead of a cloud load balancer, so I’m not sure if that’s what broke it—or if it's just an Istio quirk.

I didn’t really need Argo exposed to the public internet anyway, so I pivoted. I exposed the UI with a basic MetalLB service and now it’s accessible on my local network at `x.x.x.247`. That’s good enough for me, since I mostly plan to use the UI for prototyping.

At work we rely entirely on the declarative setup—and that’s my long-term goal here as well.

---

## Problem 2: Repo Organization & Sidecar Gotchas

My first attempt to deploy apps with ArgoCD ran into issues—mainly due to how my repositories were structured. So I spun up a new repo just for Kubernetes manifests:  
👉 [Ditmanson/argocluster](https://github.com/Ditmanson/argocluster)

(Not my best repo name, but naming is hard.)

Right now, I’m focusing on one namespace: `hugo`. I moved all related manifests—Gateways, VirtualServices, PVCs, etc.—into a dedicated directory under that namespace.

The next hiccup? Istio sidecar injection. Since Argo applies manifests declaratively, the namespace needs to be labeled for sidecar injection **before** the workloads are deployed. Otherwise, your pods won’t be part of the mesh.

To solve this, I included a manifest for the namespace itself in the repo. If you're doing something similar, apply the namespace resource first. Or fix it post-deploy with:

    kubectl rollout restart deployment -n hugo

---

## The ArgoCD Application Manifest

Here’s what my ArgoCD `Application` manifest looks like:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: hugoapps
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/Ditmanson/argocluster
    targetRevision: HEAD
    path: hugo
  destination:
    server: https://kubernetes.default.svc
    namespace: hugo
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

The `syncPolicy` block isn’t strictly necessary—especially if you prefer clicking the sync button manually—but once I added it, everything synced automatically and cleanly. All green hearts. ✨

---

## Bonus: ArgoCD Views

Here’s what it looks like in the ArgoCD UI:

![Argo Applications](/argocd/argocdapplications.png)

I wish I had seen a diagram like this when I was struggling with Istio ingress. It’s not only useful—it also looks cool.

![Argo Network](/argocd/argonetwork.png)

---

That’s it for day one with Argo. Next step: onboard more namespaces, polish up my repo structure, and figure out what belongs in Argo versus what gets handled upstream by Helm or Kustomize.

