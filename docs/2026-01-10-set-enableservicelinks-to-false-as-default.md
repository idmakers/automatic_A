---
title: "Set enableServiceLinks to False as Default in Kubernetes"
tags:
  - kubernetes
  - service-links
---

# Set enableServiceLinks to False as Default in Kubernetes

## Core Problem

The current behavior of Kubernetes injects service link environment variables into every pod, creating three environment variables for every service in a respective pod's namespace. This can lead to issues in larger namespaces where it causes pods to crash due to too many environmental variables.

## Solution & Analysis

To address this issue, we propose setting `enableServiceLinks` to false by default. This change will prevent the injection of service link environment variables into every pod, reducing the risk of environmental variable overload and potential pod crashes.

```yml
apiVersion: v1
kind: PodTemplateSpec
metadata:
  annotations:
    kubernetes.io/enable-service-links: "false"
spec:
  containers:
  - name: my-container
    image: my-image
```

In the above example, `kubernetes.io/enable-service-links` annotation is set to false, which will disable service link environment variables injection into this pod.

To make this change a default setting for all pods in a namespace, you can update the PodTemplateSpec:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-deployment
spec:
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-container
        image: my-image
      podTemplateSpec:
        spec:
          containers:
          - name: my-container
            image: my-image
          annotations:
            kubernetes.io/enable-service-links: "false"
```

## Conclusion

By setting `enableServiceLinks` to false by default, we can reduce the risk of environmental variable overload and potential pod crashes in larger namespaces. This change is a crucial step towards improving the overall stability and performance of Kubernetes clusters.

## Reference
- [Source](https://github.com/kubernetes/kubernetes/issues/121787)