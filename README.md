# kube-capacity

[![Go Report Card](https://goreportcard.com/badge/github.com/robscott/kube-capacity?v=1)](https://goreportcard.com/report/github.com/robscott/kube-capacity) [![CircleCI](https://circleci.com/gh/robscott/kube-capacity.svg?style=svg)](https://circleci.com/gh/robscott/kube-capacity)

This is a simple CLI that provides an overview of the resource requests, limits, and utilization in a Kubernetes cluster. It attempts to combine the best parts of the output from `kubectl top` and `kubectl describe` into an easy to use CLI focused on cluster resources.

## Installation
Go binaries are automatically built with each release by [GoReleaser](https://github.com/goreleaser/goreleaser). These can be accessed on the GitHub [releases page](https://github.com/robscott/kube-capacity/releases) for this project.

## Current dependencies:
A kubernetes cluster with [kube-metrics](https://github.com/kubernetes-incubator/metrics-server) installed.  A [helm chart](https://github.com/helm/charts/tree/master/stable/metrics-server) to install `kube-metrics` is available.

### Homebrew
This project can also be installed with Homebrew:
```
brew tap robscott/tap
brew install robscott/tap/kube-capacity
```

## Usage
By default, kube-capacity will output a list of nodes with the total CPU and Memory resource requests and limits for all the pods running on them. For clusters with more than one node, the first line will also include cluster wide totals. That output will look something like this:

```
kube-capacity

NODE              CPU REQUESTS    CPU LIMITS    MEMORY REQUESTS    MEMORY LIMITS
*                 560m (28%)      130m (7%)     572Mi (9%)         770Mi (13%)
example-node-1    220m (22%)      10m (1%)      192Mi (6%)         360Mi (12%)
example-node-2    340m (34%)      120m (12%)    380Mi (13%)        410Mi (14%)
```

### Including Pods
For more detailed output, kube-capacity can include pods in the output. When `-p` or `--pods` are passed to kube-capacity, it will include pod specific output that looks like this:

```
kube-capacity --pods

NODE              NAMESPACE     POD                   CPU REQUESTS    CPU LIMITS    MEMORY REQUESTS    MEMORY LIMITS
*                 *             *                     560m (28%)      780m (38%)    572Mi (9%)         770Mi (13%)

example-node-1    *             *                     220m (22%)      320m (32%)    192Mi (6%)         360Mi (12%)
example-node-1    kube-system   metrics-server-lwc6z  100m (10%)      200m (20%)    100Mi (3%)         200Mi (7%)
example-node-1    kube-system   coredns-7b5bcb98f8    120m (12%)      120m (12%)    92Mi (3%)          160Mi (5%)

example-node-2    *             *                     340m (34%)      460m (46%)    380Mi (13%)        410Mi (14%)
example-node-2    kube-system   kube-proxy-3ki7       200m (20%)      280m (28%)    210Mi (7%)         210Mi (7%)
example-node-2    tiller        tiller-deploy         140m (14%)      180m (18%)    170Mi (5%)         200Mi (7%)
```

### Including Utilization
To help understand how resource utilization compares to configured requests and limits, kube-capacity can include utilization metrics in the output. It's important to note that this output relies on [metrics-server](https://github.com/kubernetes-incubator/metrics-server) functioning correctly in your cluster. When `-u` or `--util` are passed to kube-capacity, it will include resource utilization information that looks like this:

```
kube-capacity --util

NODE              CPU REQUESTS    CPU LIMITS    CPU UTIL    MEMORY REQUESTS    MEMORY LIMITS   MEMORY UTIL
*                 560m (28%)      130m (7%)     40m (2%)    572Mi (9%)         770Mi (13%)     470Mi (8%)
example-node-1    220m (22%)      10m (1%)      10m (1%)    192Mi (6%)         360Mi (12%)     210Mi (7%)
example-node-2    340m (34%)      120m (12%)    30m (3%)    380Mi (13%)        410Mi (14%)     260Mi (9%)
```

### Including Pods and Utilization
For more detailed output, kube-capacity can include both pods and resource utilization in the output. When `--util` and `--pods` are passed to kube-capacity, it will result in a wide output that looks like this:

```
kube-capacity --pods --util

NODE              NAMESPACE     POD                   CPU REQUESTS    CPU LIMITS   CPU UTIL     MEMORY REQUESTS    MEMORY LIMITS   MEMORY UTIL
*                 *             *                     560m (28%)      780m (38%)   340m (17%)   572Mi (9%)         770Mi (13%)     470Mi (8%)

example-node-1    *             *                     220m (22%)      320m (32%)   160m (16%)   192Mi (6%)         360Mi (12%)     210Mi (7%)
example-node-1    kube-system   metrics-server-lwc6z  100m (10%)      200m (20%)   70m (7%)     100Mi (3%)         200Mi (7%)      120Mi (4%)
example-node-1    kube-system   coredns-7b5bcb98f8    120m (12%)      120m (12%)   90m (9%)     92Mi (3%)          160Mi (5%)      90Mi (3%)

example-node-2    *             *                     340m (34%)      460m (46%)   180m (18%)   380Mi (13%)        410Mi (14%)     260Mi (9%)
example-node-2    kube-system   kube-proxy-3ki7       200m (20%)      280m (28%)   110m (11%)   210Mi (7%)         210Mi (7%)      120Mi (4%)
example-node-2    tiller        tiller-deploy         140m (14%)      180m (18%)   70m (7%)     170Mi (6%)         200Mi (7%)      140Mi (5%)
```

It's worth noting that utilization numbers from pods will likely not add up to the total node utilization numbers. Unlike request and limit numbers where node and cluster level numbers represent a sum of pod values, node metrics come directly from metrics-server and will likely include other forms of resource utilization.

## Kubernetes Configuration
If a `KUBECONFIG` environment variable is specified, kube-capacity will attempt to use the config at that path, otherwise it will default to `~/.kube/config`.

## Similar Projects
There are already some great projects out there that have similar goals.

- [kube-resource-report](https://github.com/hjacobs/kube-resource-report): generates HTML/CSS report for resource requests and limits across multiple clusters.
- [kubetop](https://github.com/LeastAuthority/kubetop): a CLI similar to top for Kubernetes, focused on resource utilization (not requests and limits).

## License
Apache License 2.0
