---
title: "Enabling Label Selector Support in Resource Quota's ScopeSelector"
tags:
  - Kubernetes
  - Quotas
  - ScopeSelector
---

# Enabling Label Selector Support in Resource Quota's ScopeSelector

## Core Problem

In Kubernetes, resource quotas are used to limit the resources available to pods and services within a cluster. The scope selector feature allows quota administrators to define specific selectors for scopes, which enables more fine-grained control over quota enforcement. However, the current implementation of scope selector only supports priority class, leaving a significant limitation in quota management between different applications running in the same namespace.

## Solution & Analysis

To enable label selector support in resource quota's scopeSelector, we need to extend the existing implementation to include label selectors as an option. This would allow quota administrators to define more specific scopes for quotas, making it easier to manage resources across different namespaces and applications.

### Proposed Implementation

```yml
apiVersion: policy/v1beta1
kind: Quota
metadata:
  name: example-quota
spec:
  hard:
    cpu: 100m
    memory: 128Mi
  scopeSelector:
    matchLabels:
      - label: team=dev
```

In the proposed implementation, we've added a new field `matchLabels` under the `scopeSelector` section. This field allows quota administrators to define specific label selectors for scopes.

```c
// Example code in C++
#include <string>
#include <map>

class ScopeSelector {
public:
  std::map<std::string, std::vector<std::string>> matchLabels;

  // Add a new method to set the match labels
  void SetMatchLabels(const std::map<std::string, std::vector<std::string>>& labels) {
    matchLabels = labels;
  }
};
```

### Benefits

Enabling label selector support in resource quota's scopeSelector would provide several benefits:

*   More fine-grained control over quota enforcement between different applications running in the same namespace.
*   Easier management of resources across different namespaces and applications.

## Conclusion

In conclusion, enabling label selector support in resource quota's scopeSelector is a crucial enhancement that would improve the flexibility and effectiveness of quota management in Kubernetes. By extending the current implementation to include label selectors as an option, quota administrators can define more specific scopes for quotas, making it easier to manage resources across different namespaces and applications.

## Reference
- [Source](https://github.com/kubernetes/kubernetes/issues/77508)