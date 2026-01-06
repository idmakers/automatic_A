---
title: "Surface Permanent Errors in Kubelet and DRA Drivers"
tags:
  - Kubernetes
  - kubelet
  - DRA drivers
---

# Surface Permanent Errors in Kubelet and DRA Drivers

## Core Problem

The current implementation of the kubelet and DRA (Daemon Requestor Agent) drivers in Kubernetes uses a retry mechanism to handle errors. However, this approach can be wasteful when dealing with permanent errors that cannot be recovered from. The issue arises because some checks made by the kubelet or a DRA driver might determine that a problem is permanent, but the current code does not provide an easy way to indicate such errors.

## Solution & Analysis

To address this issue, we need to introduce a way to surface permanent errors in the gRPC interface and support marking pods as permanently failed in the kubelet. Here's a proposed solution:

### Indicating Permanent Errors

We can extend the `Status` enum in the Kubernetes API to include a new field called `PermanentlyFailed`. This field would be set to `true` when a node or pod is determined to be permanently failed.

```python
from googleapis import errors

class NodeStatus:
    # ... existing fields ...
    PermanentlyFailed = errors.Code.PERMANENTLY_FAILED
```

### Support for Permanent Failures in Kubelet

We can add a new method called `SetPermanentFailure` to the kubelet's API, which would mark a pod as permanently failed.

```python
import grpc

class KubeletStub(grpc.Stub):
    # ... existing methods ...
    def SetPermanentFailure(self, request):
        # Mark the pod as permanently failed
        self._logger.debug(f"Marking pod {request.pod_name} as permanently failed")
        # Update the node's status in the database
        self._update_node_status(request.node_name)
```

### Client-Side Updates

To take advantage of this new feature, we would need to update the client-side code that interacts with the kubelet. This could involve adding a `PermanentFailure` field to the `PodStatus` struct.

```c
// pod_status.h
struct PodStatus {
    // ... existing fields ...
    bool PermanentFailure;
};

// client.cc
void UpdatePodStatus(PodStatus* status) {
    // Set the PermanentFailure field
    status->PermanentFailure = true;
}

int main() {
    // ... create a new PodStatus object ...
    UpdatePodStatus(status);
    // ... send the updated pod status to the kubelet ...
}
```

## Conclusion

By introducing a way to surface permanent errors in the gRPC interface and supporting marking pods as permanently failed in the kubelet, we can avoid wasting resources on retrying failed operations. This change would make it easier for users to diagnose and troubleshoot issues related to node or pod failures.

## Reference
- [Source](https://github.com/kubernetes/kubernetes/issues/125542)