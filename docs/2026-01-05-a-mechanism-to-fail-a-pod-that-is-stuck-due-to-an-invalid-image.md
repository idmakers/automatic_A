---
title: "Detecting and Failing Stuck Pods Due to Invalid Images"
tags:
  - Kubernetes
  - Pod Failure
  - Image Validation
---

# Detecting and Failing Stuck Pods Due to Invalid Images

## Core Problem
When a Pod fails to pull an image due to invalid or non-existent image names, it can get stuck in the `Pending` phase indefinitely. This issue is particularly problematic for Jobs submitted through queueing systems, where the delay between job submission and pod creation can be hours or days. Stuck Pods block resources in the cluster, preventing other pending Jobs from starting.

## Solution & Analysis
To address this issue, we need to implement a mechanism that detects when an image pull has failed for a number of times (configurable) and sets the Pod into phase=Failed.

One possible approach is to create a custom Kubernetes webhook that listens for the `ContainerStatusUpdated` event. When an image pull fails, the webhook updates the container status with a failure reason and increments a counter for the failed pulls. If the counter exceeds a configurable threshold, the webhook sets the Pod's phase to Failed.

```yml
# webhook.yaml
apiVersion: apiextensions.k8s.io/v1
kind: CustomResourceDefinition
metadata:
  name: imagevalidationwebhook.crd
spec:
  group: imagevalidation
  versions:
    - name: v1alpha1
      served: true
      storage: true
      validated: false
  scope: Namespaced
  names:
    kind: ImageValidationWebhook
    plural: imagevalidationwebhooks
    singular: imagevalidationwebhook
    group: imagevalidation
  validation:
    openAPIV3Schema:
      type: object
      properties:
        name:
          type: string
        namespace:
          type: string
        event_type:
          type: string
          enum: [ContainerStatusUpdated]
        container_status_updated:
          type: ContainerStatusUpdated

# webhook implementation (go)
package main

import (
	"context"
	"fmt"
	"log"

	metav1 "k8s.io/apimachinery/pkg/apis/meta/v1"
	"k8s.io/client-go/informers"
	"k8s.io/client-go/kubernetes"
	"k8s.io/client-go/rest"
)

type ImageValidationWebhook struct {
 Metav1    *metav1.ObjectMeta
 Container StatusUpdated *ContainerStatusUpdated
}

func (i *ImageValidationWebhook) Create(ctx context.Context, req *api.v1.CreateEvent) (*api.v1.CreateEvent, error) {
	// ...
	return nil, nil // implementation omitted for brevity
}

type ContainerStatusUpdated struct {
	ContainerStatus []ContainerStatus `json:"containerStatus"`
}

func (i *ImageValidationWebhook) Update(ctx context.Context, req *api.v1.UpdateEvent) (*api.v1.UpdateEvent, error) {
	// Get the Pod and container status
	pod := req.Object
	container := pod.Status.Containers[0]

	// Check if the image pull has failed
	if container.State.Waiting != nil && container.State.Waiting.Type == "ImagePullBackoff" {
		// Increment the failure counter
		failureCounter = failureCounter + 1

		// Set the Pod's phase to Failed
		pod.Status.Phase = "Failed"
	}

	return nil, nil // implementation omitted for brevity
}

var (
	failureCounter int64 = 0
)

func main() {
	// Create a Kubernetes client and webhook server
	clientset := kubernetes.NewForConfig(context.Background(), rest.InClusterConfig())
	informer := informers.NewSharedInformerFactory(clientset, 0)
	webhookServer := webhookserver.NewWebhookServer(clientset, informer)
}

```

## Conclusion
Implementing a custom Kubernetes webhook to detect and fail stuck Pods due to invalid images can help prevent resource blockages in queueing systems. By monitoring image pull failures and setting the Pod's phase to Failed when a configurable threshold is exceeded, we can ensure that resources are freed up for other pending Jobs.

## Reference
- [Source](https://github.com/kubernetes/kubernetes/issues/122300)