---
title: "Defaulting Service Links to Disabled in Kubernetes"
tags:
  - kubernetes
  - service links
  - configuration
---

# Defaulting Service Links to Disabled in Kubernetes

## Core Problem
The current behavior of injecting environment variables for service links into every pod, regardless of their relationship, is causing issues in larger namespaces. This can lead to pods crashing due to excessive environmental variables.

## Solution & Analysis
To address this issue, we propose setting `enableServiceLinks` to `false` by default. This change will prevent the injection of environment variables for service links, reducing the risk of pod crashes in large namespaces.

### Code Example

```yml
apiVersion: v1
kind: PodTemplateSpec
metadata:
  name: example-pod-template
spec:
  containers:
  - name: example-container
    image: example-image
  serviceLink:
    enabled: false
```

In this example, the `serviceLink` field is set to `false`, disabling the injection of environment variables for service links.

### Configuration

To implement this change, we need to update the Kubernetes configuration. The `enableServiceLinks` field can be added to the `PodTemplateSpec` resource.

```yml
apiVersion: v1
kind: PodTemplateSpec
metadata:
  name: example-pod-template
spec:
  containers:
  - name: example-container
    image: example-image
  serviceLink:
    enabled: false
```

This configuration change can be applied to the `PodTemplateSpec` resource by updating the Kubernetes manifest.

## Conclusion
By setting `enableServiceLinks` to `false` by default, we can reduce the risk of pod crashes in large namespaces. This change will improve the stability and reliability of Kubernetes clusters, making them better suited for large-scale deployments.

## Reference
- [Source](https://github.com/kubernetes/kubernetes/issues/121787)