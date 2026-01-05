---
title: "Detecting and Handling Stuck Pods due to Invalid Image Pulls"
tags:
  - Kubernetes
  - Pod Failure
  - Image Pull Issues
---

# Detecting and Handling Stuck Pods due to Invalid Image Pulls

## Core Problem
When a Pod is stuck in the `Pending` phase due to an invalid image pull, it can cause resource blockages and prevent other pending Jobs from starting. This issue is particularly problematic in queued environments where jobs may not be submitted for hours or even days after creation.

## Solution & Analysis
To address this issue, we propose introducing a mechanism that sets a Pod into the `Failed` phase when the image pull has failed multiple times. This can be achieved by creating a custom container runtime configuration and using the `imagePullPolicy` field to specify the desired behavior.

```yml
apiVersion: v1
kind: ConfigMap
metadata:
  name: custom-container-runtime-config
spec:
  data:
    containerd.conf: |
      [containerd]
      image_pull_policy = "failure"
```

We can then use this configuration to create a custom container runtime for our Pod. We'll also need to introduce a new `imagePullFailureCount` field to track the number of failed image pulls.

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: example-deployment
spec:
  selector:
    matchLabels:
      app: example-app
  template:
    metadata:
      labels:
        app: example-app
    spec:
      containers:
      - name: example-container
        imagePullPolicy: "failure"
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
        containerdConfig:
          name: custom-config
          options: |
            [containerd]
            imagePullFailureCount = 3
```

To implement this behavior, we can create a custom `ImageBuilder` that tracks the number of failed image pulls and updates the `imagePullFailureCount` field accordingly.

```go
package main

import (
	"context"
	"fmt"
	"log"

	metav1 "k8s.io/apimachinery/pkg/apis/meta/v1"
	"k8s.io/client-go/informers"
)

type ImageBuilder struct {
	client  *Client
	failureCount int
}

func (ib *ImageBuilder) Build(ctx context.Context, image string) (*Image, error) {
	// Simulate an invalid image pull
	if ib.failureCount < 3 {
		ib.failureCount++
		return nil, fmt.Errorf("invalid image pull")
	}
	ib.failureCount = 0 // Reset the failure count

	return &Image{
		Name:     image,
		Hash:     "example-hash",
		Size:     100,
	}, nil
}

func (ib *ImageBuilder) UpdateFailureCount(ctx context.Context, failureCount int) {
	ib.failureCount += failureCount
}
```

We can then use this `ImageBuilder` to build our Pod's image and update the `imagePullFailureCount` field accordingly.

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: example-deployment
spec:
  selector:
    matchLabels:
      app: example-app
  template:
    metadata:
      labels:
        app: example-app
    spec:
      containers:
      - name: example-container
        imagePullPolicy: "failure"
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
        containerdConfig:
          name: custom-config

		// Create a custom ImageBuilder instance
		imageBuilder := &ImageBuilder{
			client: &Client{},
			failureCount: 0,
		}

		// Use the ImageBuilder to build the Pod's image
		image, err := imageBuilder.Build(context.Background(), "example-image")
		if err != nil {
			log.Println(err)
			return
		}
	}
```

## Conclusion
By introducing a custom container runtime configuration and tracking the number of failed image pulls, we can detect and handle stuck Pods due to invalid image pulls. This solution provides a more robust way to handle image pull failures and prevents resource blockages in queued environments.

## Reference
- [Source](https://github.com/kubernetes/kubernetes/issues/122300)