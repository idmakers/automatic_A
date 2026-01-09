---
title: "Enabling Scalable PDB Constraints in Kubernetes"
tags:
  - Kubernetes
  - Horizontal Pod Autoscaling
  - Persistent Volumes
  - Disruption Budget

---

# Enabling Scalable PDB Constraints in Kubernetes

## Core Problem

When using a disruption budget (PDB) with horizontal pod autoscaling (HPA), scenarios can occur where the HPA scales up to meet constraints, but the new replicas are not immediately available to replace terminated old ones. This can lead to a situation where disruptions get "stuck" if there is high load.

## Solution & Analysis

To enable scalable PDB constraints in Kubernetes, we need to modify the HPA behavior when scaling up. Currently, when `minAvailable` is set to 1 and `maxReplicas` is set to N, the HPA will scale up to 1 replica first, but it may not immediately scale up additional replicas.

One possible solution is to use a combination of `maxReplicas` and `minReadySeconds` to control how quickly new replicas are brought online. We can also use the `scaleDownPolicy` and `preference` fields in the HPA configuration to ensure that old replicas are terminated after a new replica has been successfully scaled up.

Here is an example of an updated HPA configuration:
```yaml
apiVersion: autoscaling/v2beta2
kind: HorizontalPodAutoscaler
metadata:
  name: my-hpa
spec:
  minReplicas: 1
  maxReplicas: 5
  scaleDownPolicy:
    stabilizationWindowSeconds: 300
    removalDelaySeconds: 30
  preference:
    minAvailable: 2
```
In this example, the HPA will scale down to a minimum of 2 replicas when `minAvailable` is set to 1. The `scaleDownPolicy` ensures that old replicas are terminated after 5 minutes (300 seconds) have passed since the last successful scaling up.

We can also use a custom `ScaleUpPolicy` to control how quickly new replicas are brought online:
```yaml
apiVersion: autoscaling/v2beta2
kind: HorizontalPodAutoscaler
metadata:
  name: my-hpa
spec:
  minReplicas: 1
  maxReplicas: 5
  scaleDownPolicy:
    stabilizationWindowSeconds: 300
    removalDelaySeconds: 30
  preference:
    minAvailable: 2
  scaleUpPolicy:
    minimumReplicasToScale: 3
```
In this example, the HPA will only start scaling up to a minimum of 3 replicas when `minAvailable` is set to 1.

## Conclusion

By modifying the HPA configuration and using a combination of `maxReplicas`, `minReadySeconds`, `scaleDownPolicy`, and `preference` fields, we can enable scalable PDB constraints in Kubernetes. This allows us to control how quickly new replicas are brought online and ensures that disruptions do not get stuck when high load is applied.

## Reference
- [Source](https://github.com/kubernetes/kubernetes/issues/93476)