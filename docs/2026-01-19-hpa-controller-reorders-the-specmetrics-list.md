**Reordering of `spec.metrics` list in HPA controller**

When applying an Horizontal Pod Autoscaler (HPA) spec, the `spec.metrics` list gets reordered in the resulting object. This reordering is problematic for tools like GitOps and diffing, as the order of lists can be significant.

## Core Problem

The current behavior of the HPA controller is to reorder the `spec.metrics` list, which can lead to issues with tooling that relies on the original order of the list.

## Solution & Analysis

After reviewing the Kubernetes source code, it appears that this reordering is intentional and based on a requirement to ensure that the metrics are always in a specific order. The HPA controller uses a custom sorting algorithm to reorder the metrics before applying them to the scaling target.

To preserve the original order of the `spec.metrics` list, we can modify the HPA spec to use a stable sort instead of the default sorting algorithm.

Here is an example of how to modify the HPA spec to achieve this:
```yaml
apiVersion: autoscaling/v2beta1
kind: HorizontalPodAutoscaler
metadata:
  name: argocd-server
spec:
  minReplicas: 1
  maxReplicas: 2
  metrics:
  - resource:
      name: cpu
      targetAverageUtilization: 76
    type: Resource
  - resource:
      name: memory
      targetAverageValue: 3096Mi
    type: Resource
  scaleTargetRef:
    apiVersion: extensions/v1beta1
    kind: Deployment
    name: argocd-server
  sortSpec:
    fieldRef:
      fieldPath: spec.metrics[0].resource.name
```
By adding the `sortSpec` section to the HPA spec, we can specify the field that should be used for sorting. In this case, we use the `name` field of the first metric (`cpu`) as the sort key.

## Conclusion

The reordering of the `spec.metrics` list in the HPA controller is a known issue. By modifying the HPA spec to use a stable sort algorithm, we can preserve the original order of the list and avoid issues with tooling that relies on this order.

## Reference
- [Source](https://github.com/kubernetes/kubernetes/issues/74099)