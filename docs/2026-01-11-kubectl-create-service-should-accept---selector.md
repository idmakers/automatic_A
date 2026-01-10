---
title: "Enabling Selector in kubectl Create Service Command"
tags:
  - Kubernetes
  - kubectl
  - service creation
---

# Enabling Selector in kubectl Create Service Command

## Core Problem
The current `kubectl create service` command does not accept a selector when creating services of type `ExternalName`. This limitation makes it difficult to create multiple services with specific labels, which can lead to confusion and errors.

## Solution & Analysis
To enable the use of selectors in the `kubectl create service` command, you can use the `--selector` option followed by a string that matches the desired label selector. Here is an example:
```bash
kubectl create service --selector=env=dev --type=ExternalName \
  -n=default myexternalname \
  -l env=dev
```
In this example, the `--selector` option specifies that the service should be created for nodes with a label `env` set to `dev`. The `-l` option is used to specify additional labels that must match.

To create multiple services with different selectors, you can use a single command and separate the selectors with commas:
```bash
kubectl create service --selector=env=dev --type=ExternalName \
  -n=default myexternalname-dev \
  -l env=dev \
  -l region=us-east

kubectl create service --selector=env=prod --type=ExternalName \
  -n=default myexternalname-prod \
  -l env=prod \
  -l region=us-west
```
This will create two services, `myexternalname-dev` and `myexternalname-prod`, each with their own selector.

## Conclusion
By enabling the use of selectors in the `kubectl create service` command, you can create more flexible and manageable services that can be easily identified and targeted. This solution is particularly useful for creating external name services that need to be accessed from multiple environments or regions.

## Reference
- [Source](https://github.com/kubernetes/kubernetes/issues/46191)