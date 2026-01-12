---
title: "Configuring imagePullPolicy to Pull Latest Image or Fall Back to Local Cache"
tags:
  - Kubernetes
  - imagePullPolicy
  - caching
---

# Configuring imagePullPolicy to Pull Latest Image or Fall Back to Local Cache

## Core Problem
The current `imagePullPolicy` only offers three options: `Always`, `IfNotPresent`, and `Never`. While these options provide flexibility, they do not cater to scenarios where developers want to pull the latest image while minimizing single points of failure in production.

## Solution & Analysis
To address this issue, we propose adding a fourth option to `imagePullPolicy` that attempts to pull the image and falls back to using a local cached copy if the image cannot be pulled.

### New Option: Pull Latest Image or Fall Back to Local Cache

The new option, which we'll call `TryLatest`, would first try to pull the latest image from the repository. If the pull is successful, it would use the newly pulled image. If the pull fails due to network issues or other reasons, it would fall back to using a local cached copy.

Here's an example of how this option could be implemented in YAML:
```yaml
spec:
  containers:
    - name: my-container
      image: <repository-url>
      imagePullPolicy: TryLatest
```
In code, the `TryLatest` option would work as follows:

1. Check if a cached copy of the image exists locally.
2. Attempt to pull the latest image from the repository.
3. If the pull is successful, use the newly pulled image.
4. If the pull fails, fall back to using the local cached copy.

### Benefits

The `TryLatest` option offers several benefits:

* Developers can pull the latest changes while minimizing single points of failure in production.
* The option provides a balance between flexibility and reliability, making it suitable for both development and production environments.
* It eliminates the need for manual caching or explicit cache management.

## Conclusion
Adding a `TryLatest` option to `imagePullPolicy` would provide developers with more flexibility and control over image management. By attempting to pull the latest image while falling back to using a local cached copy, this option minimizes single points of failure in production while catering to development needs.

## Reference
- [Source](https://github.com/kubernetes/kubernetes/issues/111822)