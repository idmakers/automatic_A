---
title: "Flexibly Pulling Images with Kubernetes"
tags:
  - imagePullPolicy
  - Kubernetes
  - container orchestration

---

# Flexibly Pulling Images with Kubernetes

## Core Problem

The current `imagePullPolicy` options in Kubernetes only provide three choices: Always, IfNotPresent, and Never. While these options serve different purposes, they do not cater to the needs of both development and production environments. In development, we need to grab the latest version of the image to test against, but this comes at the cost of having a single point of failure in the repository. On the other hand, in production, we want to minimize single points of failure by using cached images whenever possible.

## Solution & Analysis

To address this issue, we propose adding a new option for `imagePullPolicy` that attempts to pull the image and falls back to using a local cached copy if the image cannot be pulled. This would provide a flexible solution that balances the need for latest changes in development with the need for reliability in production.

Here's an example of how this new option could be implemented:
```yaml
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
        image: <image-name>
        imagePullPolicy:
          policy: cachedIfFailed
```
In this example, the `cachedIfFailed` policy attempts to pull the image first and falls back to using a local cached copy if the image cannot be pulled. This provides a balance between grabbing the latest changes in development and minimizing single points of failure in production.

## Conclusion

The addition of a new `imagePullPolicy` option that attempts to pull the image and falls back to using a local cached copy would provide a flexible solution for both development and production environments. By offering this additional option, Kubernetes can cater to the diverse needs of its users and provide a more robust container orchestration experience.

## Reference
- [Source](https://github.com/kubernetes/kubernetes/issues/111822)