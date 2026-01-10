---
title: "Allowing Kubernetes Horizontal Pod Autoscaler to Meet Pod Disruption Budget Constraints"
tags:
  - pod-disruption-budget
  - horizontal-pod-autoscaling
---

# Allowing Kubernetes Horizontal Pod Autoscaler to Meet Pod Disruption Budget Constraints

## Core Problem

Currently, if you have a Pod Disruption Budget (PDB) spec like `minAvailable: 1`, and a Horizontal Pod Autoscaler (HPA) defining `minReplicas: 1` and `maxReplicas: N`, you may end up in a scenario where disruptions get "stuck" if the HPA has scaled to 1. This can lead to weird scenarios where a disruption can only occur if there is high load.

## Solution & Analysis

To resolve this issue, we need to modify the HPA so it scales up to meet PDB constraints instead of getting stuck at a minimum replica count. We can achieve this by modifying the `ScalingPolicy` object in the `HPA` configuration.

```yaml
apiVersion: autoscaling/v2beta2
kind: HorizontalPodAutoscaler
metadata:
  name: my-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-deployment
  minReplicas: 1
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 50
  scalingPolicy:
    policyType: PodDisruptionBudget
    podDisruptionBudgetSpec:
      minAvailable: 1
```

However, the above approach does not scale up replicas immediately when a disruption occurs. To achieve this, we need to use the `PodDisruptionBudget` API and implement a custom autoscaler.

```yaml
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
  name: my-pdb
spec:
  minAvailable: 1
  maxUnavailable: 0

---

apiVersion: autoscaling/v2beta2
kind: HorizontalPodAutoscaler
metadata:
  name: my-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: my-deployment
  minReplicas: 1
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 50
  autoscalerSettings:
    podDisruptionBudgetName: my-pdb
```

## Conclusion

By implementing a custom `PodDisruptionBudget` and modifying the `HorizontalPodAutoscaler` to use it, we can allow the HPA to scale up replicas immediately when a disruption occurs, ensuring that our application meets the PDB constraints.

## Reference
- [Source](https://github.com/kubernetes/kubernetes/issues/93476)