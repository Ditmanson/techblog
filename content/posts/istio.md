---
title: "Ingress"
date: 2025-05-26
draft: false
description: "A summary of my ingress set-up"
tags:
  - k8s
  - k3s
  - ingress
  - istio
  - nginx
---

# My Istio Ingress Setup (and Some Lessons Learned)

Ingress in Kubernetes isn’t *one-size-fits-all*. There are a dozen different paths you can take, and this is just one of them. I opted for the [Istio sidecar method](https://istio.io/latest/docs/setup/getting-started/) and followed the [TLS Gateway instructions](https://istio.io/latest/docs/tasks/traffic-management/ingress/secure-ingress/) to secure it. Here's what I learned.

---

## Assumptions

1. The load balancer has its own external IP.
2. You already have deployments with pods and services up and running.

---

## Pods in Play

First off, here’s what my Istio and NGINX pods look like:

    $ k get pods -n istio-system -o wide
    NAME                                    READY   STATUS    RESTARTS   AGE   IP           NODE      NOMINATED NODE   READINESS GATES
    istio-ingressgateway-7f7bcf6bb9-kf444   1/1     Running   0          8d    10.42.0.71   tdebian   <none>           <none>
    istiod-85c68488c4-94s9v                 1/1     Running   0          8d    10.42.0.70   tdebian   <none>           <none>

In the `hugo` namespace, I accidentally created a race condition. I copied a service config and forgot to update one of the labels, which meant both services were routing traffic to both pods. Oops.

    $ k get all -n hugo
    NAME                              READY   STATUS    RESTARTS   AGE
    pod/nginx-hugo-946ff545b-2mclc    2/2     Running   0          5h21m
    pod/nginx-tech-7449666dd7-d9q9r   2/2     Running   0          5h20m

    NAME                     TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)   AGE
    service/nginx-svc-hugo   ClusterIP   10.43.213.158   <none>        80/TCP    5h21m
    service/nginx-svc-tech   ClusterIP   10.43.88.164    <none>        80/TCP    5h20m

    NAME                         READY   UP-TO-DATE   AVAILABLE   AGE
    deployment.apps/nginx-hugo   1/1     1            1           5h21m
    deployment.apps/nginx-tech   1/1     1            1           5h20m

---

## TLS Secrets Gotcha

Here's a catch I ran into with TLS: the secrets *must* go in the `istio-system` namespace, not alongside your deployments.

I originally placed them in the same namespace as my apps, thinking that since the gateway referenced them, they’d work. But nope — no TLS until they were in `istio-system`. That’s probably because it’s the `istio-ingressgateway` pod that actually uses them.

    $ k get secret -n istio-system
    NAME               TYPE                DATA   AGE
    istio-ca-secret    istio.io/ca-root    5      8d
    tblog-credential   kubernetes.io/tls   2      7h3m
    tech-credential    kubernetes.io/tls   2      7h1m

🔒 I’m using self-signed certs for now, so the browser still shows the site as unsafe.  
**TODO**: Switch to cert-manager or upload proper certificates.

---

## Gateway Config

I started with the Istio example and tweaked it from there — port numbers, hosts, credentials, etc. This version routes based on hostname and supports both HTTP and HTTPS.

```yaml
apiVersion: networking.istio.io/v1
kind: Gateway
metadata:
  name: nginx-gateway
  namespace: hugo
spec:
  selector:
    istio: ingressgateway
  servers:
    - port:
        number: 443
        name: https-tblog
        protocol: HTTPS
      tls:
        mode: SIMPLE
        credentialName: tblog-credential
      hosts:
        - "tblog.tdebian.com"
    - port:
        number: 443
        name: https-tech
        protocol: HTTPS
      tls:
        mode: SIMPLE
        credentialName: tech-credential
      hosts:
        - "tech.tdebian.com"
    - port:
        number: 80
        name: http-tblog
        protocol: HTTP
      hosts:
        - "tblog.tdebian.com"
    - port:
        number: 80
        name: http-tech
        protocol: HTTP
      hosts:
        - "tech.tdebian.com"
```

❓ **TODO**: Look into other TLS modes like `MUTUAL` and `PASSTHROUGH` and when you'd want to use them.

---

## VirtualService

For my `VirtualService`, I eventually simplified things. At first, I thought I could use shorthand for the `host`, but it didn’t work for me. I had to use the full FQDN (`.svc.cluster.local`) to get traffic routed properly.

```yaml
apiVersion: networking.istio.io/v1
kind: VirtualService
metadata:
  name: tblogtdebian
  namespace: hugo
spec:
  hosts:
    - "tblog.tdebian.com"
  gateways:
    - nginx-gateway
  http:
    - route:
        - destination:
            host: nginx-svc-hugo.hugo.svc.cluster.local
```

