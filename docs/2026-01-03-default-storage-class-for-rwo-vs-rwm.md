---
title: "Choosing the Right Default Storage Class for RWO and RWM"
tags:
  - Kubernetes Storage Classes
  - ReadWriteOnce vs ReadWriteMany
---

# Choosing the Right Default Storage Class for RWO and RWM

## Core Problem

In a Kubernetes cluster, multiple storage classes are available, but it's often unclear which one should be set as the default. This can lead to confusion when deploying Persistent Volumes (PVs) with ReadWriteOnce (RWO) or ReadWriteMany (RWM) access modes.

## Solution & Analysis

To solve this issue, we can leverage a custom annotation `storageclass.kubernetes.io/is-default-class` and use it to determine the default storage class based on the user's request for RWO or RWM.

### Step 1: Create a Custom Annotation

We'll create a new annotation that sets a flag indicating whether the storage class is for RWO or RWM.
```yaml
apiVersion: storage.k8s.io/v1beta1
kind: StorageClass
metadata:
  name: rwo-storageclass
spec:
  volumeMode: Filesystem
  storageClassName: local-storage
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteOnce
  annotations:
    "storageclass.kubernetes.io/is-default-class": "true"
```

### Step 2: Create a Custom Ingress Controller

We'll create a custom ingress controller that checks the annotation and sets the default storage class accordingly.
```go
// ingress-controller.go
package main

import (
	"context"
	"fmt"
	"log"

	metav1 "k8s.io/apimachinery/pkg/apis/meta/v1"
	"k8s.io/client-go/informers"
	"k8s.io/client-go/kubernetes"
	"sigs.kubernetes.io/ingress-annotations"
)

func (ic *IngressController) getStorageClass(ctx context.Context, storageClass string) (*metav1.ObjectMeta, error) {
annotation := ingressAnnotations.GetAnnotation(context.TODO(), ic.namespace, "storageclass.kubernetes.io/is-default-class")
if annotation == "true" && storageClass != "" {
return &metav1.ObjectMeta{
	Name:      storageClass,
	Namespace: ic.namespace,
	Kind:      "StorageClass"
}, nil
}
return nil, fmt.Errorf("unknown storage class %s", storageClass)
}

func (ic *IngressController) setDefaultStorageClass(ctx context.Context, clientSet kubernetes.Clientset) error {
storageClasses, err := clientSet.StorageV1().StorageClasses(ic.namespace).List(context.TODO(), metav1.ListOptions{
	Selector: map[string]string{
			"storageclass.kubernetes.io/is-default-class": "true",
		},
	})
if err != nil {
return err
}
for _, storageClass := range storageClasses.Items {
defaultStorageClass, err := ic.getStorageClass(ctx, storageClass.Name)
if err == nil && defaultStorageClass != nil {
clientSet.StorageV1().StorageClasses(ic.namespace).Update(context.TODO(), defaultStorageClass, metav1.UpdateOptions{})
}
}
return nil
}
```

### Step 3: Update the Kubernetes API

We'll update the Kubernetes API to include our custom annotation and storage class.
```go
// api.go
package main

import (
	"k8s.io/apimachinery/pkg/apis/meta/v1"
)

func (s *StorageClassAPI) AddAnnotation(annotation string, namespace string, obj *metav1.ObjectMeta) {
	s.APIClient.StorageV1().NamespacedObjects(namespace).Update(context.TODO(), s.APIKey, func(options metav1.UpdateOptions) {
		options.FieldPath = ""
	})
}

func (s *StorageClassAPI) GetAnnotation(namespace string, annotationName string) (string, error) {
	return s.APIClient.StorageV1().NamespacedObjects(namespace).GetAnnotations(context.TODO()).Get(annotationName)
}
```

## Conclusion

By leveraging a custom annotation and using it to determine the default storage class based on the user's request for RWO or RWM, we can simplify the process of choosing the right storage class for our Persistent Volumes.