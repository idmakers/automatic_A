**Excessive conntrack Cleanup Causes High Memory and CPU Usage in Kubernetes**

### What Happened?

In Kubernetes 1.32, changes to Services or Pods that expose UDP ports trigger a full conntrack cleanup, leading to high resource consumption. This issue affects kube-proxy instances, causing them to consume up to 12 GB of memory and 1.5 CPU cores.

### What Did You Expect to Happen?

We expected kube-proxy to handle conntrack cleanup in a more efficient and targeted way. Ideally, it should limit its cleanup to entries relevant to the specific changed UDP endpoint or provide a way to configure or disable this aggressive cleanup process.

### How Can We Reproduce It (as Minimally and Precisely as Possible)?

1. Deploy multiple Pods that generate a high volume of DNS requests.
2. Observe kube-proxy resource usage (memory and CPU) on the node.
3. Delete or update the CoreDNS Pod, which also uses UDP DNS.
4. Watch the logs and resource usage of kube-proxy closely, noting the surge in memory (potentially up to 12 GB) and CPU usage as it performs the conntrack cleanup.

### Anything Else We Need to Know?

### Kubernetes Version

```console
$ kubectl version
Client Version: v1.31.2
Kustomize Version: v5.4.2
Server Version: v1.32.0-eks-5ca49cb
```

### Cloud Provider

AWS

### OS Version

```console
# On Linux: Amazon Linux 2
5.10.230-223.885.amzn2.aarch64
```

### Install Tools

EKS

### Container Runtime (CRI) and Version (if applicable)

containerd://1.7.23

### Related Plugins (CNI, CSI, ...) and Versions (if applicable)

kube-proxy:v1.32.0-minimal-eksbuild.2

### Top Solution/Comment

/sig network

## Reference
- [Source](https://github.com/kubernetes/kubernetes/issues/129982)