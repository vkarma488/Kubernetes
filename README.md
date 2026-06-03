# Kubernetes
Kubernetes
Markdown
# Kubernetes Local Sandbox & Verification Guide

This repository contains the deployment manifests and verification steps for running a scalable application inside a local Kubernetes cluster (Docker/WSL environment).

## Cluster Architecture & Pod Topography

When scaling the application deployment to 3 replicas, the `kube-scheduler` distributes the workloads across the available infrastructure. In a single-worker local environment, the pods are co-located on the same underlying worker node:

```text
NAME                      READY   STATUS    RESTARTS   AGE     IP           NODE
my-app-59854d5646-6qtqj   1/1     Running   0          8m37s   10.244.1.5   desktop-worker
my-app-59854d5646-9wwj4   1/1     Running   0          8m37s   10.244.1.4   desktop-worker
my-app-59854d5646-ft8k8   1/1     Running   0          9m23s   10.244.1.3   desktop-worker

Verification & Observability Playbook
1. Multi-Pod Log Aggregation
To stream and monitor incoming traffic across all running replicas simultaneously while identifying the specific pod handling the request, use the label selector with the --prefix flag:

Bash
kubectl logs -l app=my-app --tail=10 -f --prefix
2. End-to-End Request Tracing (Pod to Node Mapping)
To trace a live HTTP request from your local browser back to the exact Kubernetes node serving it, follow these steps:

Run the prefixed log stream command above.

Hit the local endpoint (via port-forward or service).

Capture the handling pod's unique identifier from the log prefix:

Plaintext
[pod/my-app-59854d5646-6qtqj] 127.0.0.1 - - [03/Jun/2026:13:38:43 +0000] "GET / HTTP/1.1" 200 896
Cross-reference the pod name with the cluster infrastructure layout using the wide output format:

Bash
kubectl get pods -o wide
Match the target pod name to its corresponding column to determine the hosting node (e.g., desktop-worker).

Future Enhancements
[ ] Implement a multi-node local cluster configuration (e.g., Kind multi-worker YAML) to demonstrate inter-node load balancing.

[ ] Expose the deployment via a native Kubernetes Service (NodePort / ClusterIP) instead of direct local port-forwarding tunnels.
