---
title: "Disable Service Links by Default in Kubernetes"
tags:
  - kubernetes
  - service-links
---

# Disable Service Links by Default in Kubernetes

## Core Problem
Service link environment variables are injected into every pod, creating three environment variables for each service in a respective pod's namespace. This behavior was observed in EKS 1.24 and can cause pods to crash due to too many environmental variables.

## Solution & Analysis
To address this issue, we propose setting `enableServiceLinks` to `false` by default. This change will prevent the injection of unnecessary environment variables into pods.

```yml
apiVersion: apiextensions.k8s.io/v1beta1
kind: CustomResourceDefinition
metadata:
  name: ServiceLink
spec:
  group: ""
  namespaced: false
  scope: Namespaced
  serviceType: "ServiceLink"
  validation:
    openAPIV3Schema:
      type: object
      properties:
        metadata:
          type: object
          properties:
            namespace:
              type: string
            name:
              type: string
          required:
            - namespace
            - name
        spec:
          type: object
          properties:
            selector:
              type: object
              properties:
                matchLabels:
                  type: object
                  properties:
                    key:
                      type: string
                    value:
                      type: string
                matchFields:
                  type: array
                  items:
                    $ref: '#/items/MatchField'
              required:
                - matchFields
            service:
              type: object
              properties:
                metadata:
                  type: object
                  properties:
                    namespace:
                      type: string
                    name:
                      type: string
                  required:
                    - namespace
                    - name
                spec:
                  type: object
                  properties:
                    hostNetwork:
                      type: boolean
          required:
            - selector
            - service
  validationRule: "validateServiceLink"
```

In addition to the above change, we also propose adding a `disableServiceLinks` field to the ServiceLink resource, which will allow administrators to opt-out of this behavior.

```yml
apiVersion: apiextensions.k8s.io/v1beta1
kind: CustomResourceDefinition
metadata:
  name: ServiceLink
spec:
  group: ""
  namespaced: false
  scope: Namespaced
  serviceType: "ServiceLink"
  validation:
    openAPIV3Schema:
      type: object
      properties:
        metadata:
          type: object
          properties:
            namespace:
              type: string
            name:
              type: string
          required:
            - namespace
            - name
        spec:
          type: object
          properties:
            selector:
              type: object
              properties:
                matchLabels:
                  type: object
                  properties:
                    key:
                      type: string
                    value:
                      type: string
                matchFields:
                  type: array
                  items:
                    $ref: '#/items/MatchField'
              required:
                - matchFields
            service:
              type: object
              properties:
                metadata:
                  type: object
                  properties:
                    namespace:
                      type: string
                    name:
                      type: string
                  required:
                    - namespace
                    - name
                spec:
                  type: object
                  properties:
                    hostNetwork:
                      type: boolean
            disableServiceLinks:
              type: boolean
          required:
            - selector
            - service
  validationRule: "validateServiceLink"
```

## Conclusion
By setting `enableServiceLinks` to `false` by default, we can prevent unnecessary environment variables from being injected into pods and improve the overall stability of Kubernetes clusters.

## Reference
- [Source](https://github.com/kubernetes/kubernetes/issues/121787)