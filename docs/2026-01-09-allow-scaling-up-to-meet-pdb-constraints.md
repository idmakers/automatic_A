---
title: "Allowing Kubernetes Horizontal Pod Autoscaling to Meet Persistent Volume Boundaries"
tags:
  - Kubernetes Autoscaling
  - PDB Constraints

---

# Allowing Kubernetes Horizontal Pod Autoscaling to Meet Persistent Volume Boundaries

## Core Problem
When using Kubernetes Horizontal Pod Autoscaling (HPA) with a Persistent Volume Bound (PDB), it's possible for the HPA to scale up beyond the minimum replica count specified in the PDB. This can lead to unexpected behavior, particularly with stateful workloads.

## Solution & Analysis

To address this issue, you need to implement an additional feature that allows the Horizontal Pod Autoscaler to respect Persistent Volume Bound constraints.

```yml
apiVersion: autoscaling/v2beta2
kind: HorizontalPodAutoscaler
metadata:
  name: my-hpa
spec:
  minReplicas: 1
  maxReplicas: 10
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-deployment
  behavior:
    scaleDown:
      stabilizationWindowSeconds: 300 # adjust to your needs
```

To achieve this, you'll need to create a custom `HPA` resource that respects the PDB constraints.

```bash
# Create a new file named hpa-respect-pdb-constraints.yaml
apiVersion: autoscaling/v2beta2
kind: HorizontalPodAutoscaler
metadata:
  name: my-hpa
spec:
  minReplicas: 1
  maxReplicas: 10
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-deployment
  selector:
    matchLabels:
      app: my-app
  behavior:
    scaleDown:
      stabilizationWindowSeconds: 300 # adjust to your needs
    scaleUp:
      stabilizationWindowSeconds: 300 # adjust to your needs
```

```yml
# Create a new file named hpa-respect-pdb-constraints.yaml (continued)
spec:
  rules:
  - selector:
     matchLabels:
       app: my-app
   actions:
   - action:
        type: UpdateReplicaCount
        value: 1
     reason: RespectPDBConstraints
```

To apply this custom HPA resource, you can use the following command:

```bash
kubectl apply -f hpa-respect-pdb-constraints.yaml
```

## Conclusion
By implementing a custom `HPA` resource that respects Persistent Volume Bound constraints, you can ensure that your stateful workloads are properly scaled and maintained within the bounds of the PDB.

## Reference
- [Source](https://github.com/kubernetes/kubernetes/issues/93476)