---
title: "Pods with Zero TerminationGracePeriod are Force-Deleted"
tags:
  - Kubernetes
  - Pod Termination
  - StatefulSets

---

# Pods with Zero TerminationGracePeriod are Force-Deleted

## Core Problem

When a pod with `spec.terminationGracePeriod: 0` is deleted without force, it's force-deleted from the API server, without kubelet killing its containers and unmounting its volumes first. This can be dangerous for StatefulSets, which guarantee that only a single replica of a Pod runs.

## Solution & Analysis

### What happened?

When a pod with `spec.terminationGracePeriod: 0` is deleted without force, it's force-deleted from the API server, without kubelet killing its containers and unmounting its volumes first.

```bash
# Run a StatefulSet with a single replica with terminationGracePeriod: 0
kubectl apply -f statefulset.yaml

# Stop kubelet on the node where the replica starts
systemctl stop kubelet

# After 5-6 minutes, node controller deletes the pod (without force), but the pod gets deleted from the API server because of terminationGracePeriod: 0
```

### What did you expect to happen?

Pods should be deleted from the API server only after their containers are killed and their volumes unmounted.

```bash
# Run a StatefulSet with a single replica with terminationGracePeriod: 1
kubectl apply -f statefulset.yaml

# Observe that a new replica starts, while the old replica is still running on the "problematic" node.
```

### Solution

To prevent this behavior, you can increase the `terminationGracePeriod` to a non-zero value. This will ensure that kubelet kills the containers and unmounts the volumes before deleting the pod.

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: my-statefulset
spec:
  selector:
    matchLabels:
      app: my-app
  serviceName: "my-service"
  replicas: 1
  terminationGracePeriodSeconds: 300 # Increase the terminationGracePeriod to a non-zero value
```

## Conclusion

When using StatefulSets with `spec.terminationGracePeriod: 0`, it's essential to be aware of the potential consequences of force-deleted pods. Increasing the `terminationGracePeriod` to a non-zero value can help prevent these issues and ensure that only a single replica of a Pod runs.

## Reference
- [Source](https://github.com/kubernetes/kubernetes/issues/120671)