---

title: "NodeLifeCycleController: all pods are marked as not ready making workload services unavailable during network partition b/w master & worker nodes"
tags:
  - Kubernetes
  - NodeLifeCycleController
  - Network Partition

---

# Understanding the NodeLifeCycleController Issue

## Core Problem
When a network partition occurs between master and worker nodes in a Kubernetes cluster, all pods are marked as not ready by the NodeLifeCycleController, leading to widespread service unavailability.

## Solution & Analysis

To reproduce this issue:

1. Host a few load balancer/clusterIP services in a k8s cluster.
2. Create a network partition between master and worker nodes using iptable rules or similar methods.
3. Each master node is deployed with components stacked together, hosting etcd, apiserver, controller-manager, cloud-controller, scheduler, cni, etc.

The load balancer external IP address will not be able to route traffic to pods since addresses are removed from the endpoint resource after the NodeLifeCycleController marking pods as not ready.

```console
# Kubernetes version
$ kubectl version
Client Version: version.Info{Major:"1", Minor:"20", GitVersion:"v1.20.0", GitCommit:"af46c47ce925f4c4ad5cc8d1fca46c7b77d13b38", GitTreeState:"clean", BuildDate:"2020-12-13T19:50:45Z", GoVersion:"go1.15.5", Compiler:"gc", Platform:"darwin/amd64"}
Server Version: version.Info{Major:"1", Minor:"20", GitVersion:"v1.20.7", GitCommit:"132a687512d7fb058d0f5890f07d4121b3f0a2e2", GitTreeState:"clean", BuildDate:"2021-05-12T12:32:49Z", GoVersion:"go1.15.12", Compiler:"gc", Platform:"linux/amd64"}
```

```console
# OS version
$ cat /etc/os-release
PRETTY_NAME="Debian GNU/Linux 10 (buster)"
NAME="Debian GNU/Linux"
VERSION_ID="10"
VERSION="10 (buster)"
VERSION_CODENAME=buster
ID=debian
HOME_URL="https://www.debian.org/"
SUPPORT_URL="https://www.debian.org/support"
BUG_REPORT_URL="https://bugs.debian.org/"

$ uname -a
Linux xxx-master-1 5.10.0-0.bpo.3-cloud-amd64 #1 SMP Debian 5.10.13-1~bpo10+1 (2021-02-11) x86_64 GNU/Linux
```

## Conclusion
This issue highlights the importance of understanding how network partitions affect pod readiness and service availability in Kubernetes clusters. By following these steps, developers can reproduce this issue and work towards implementing a solution to mitigate its impact on workload services.

## Reference
- [Source](https://github.com/kubernetes/kubernetes/issues/105617)