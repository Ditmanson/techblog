---
title: "Load balancer"
date: 2025-04-12T22:00:00Z
draft: false
---

I wanted to run all my apps off kubernetes instead of the more popular homelab option of docker_compose. The intention is to get better at work; however, most loadbalancers don't work on bare metal. After eventually discovering this I installed metallb and removed the stock loadbalancer form k3s. 

```yaml
---
apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: first-pool
  namespace: metallb-system
spec:
  addresses:
    - x.x.x.x-x.x.x.y
---
```
```yaml
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-svc
  namespace: hugo
  labels:
    app: nginx-hugo
spec:
  type: LoadBalancer
  selector:
    app: nginx-hugo
  ports:
    - name: http
      port: 80
      targetPort: 80
    - name: https
      port: 443
      targetPort: 443
---
```
```yaml
---
apiVersion: metallb.io/v1beta1
kind: L2Advertisement
metadata:
  name: first-pool
  namespace: metallb-system
---
