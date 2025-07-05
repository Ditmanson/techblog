---
title: WebServer
date: 2025-05-10
draft: false
tags: 
    - tech
    - yaml
    - nginx
    - pods
---
Not much to show here, but here's my nginx pod. I'll switch this to a deployment after I get a second node up and running
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-hugo
  namespace: hugo
  labels:
    app: nginx-hugo
spec:
  containers:
  - name: nginx
    image: nginx:latest
    ports:
    - containerPort: 80
    - containerPort: 443
    volumeMounts:
    - name: nas-volume
      mountPath: /usr/share/nginx/html
  volumes:
    - name: nas-volume
      persistentVolumeClaim:
        claimName: nas-pvc
```
