# Civo Navigate Berlin 2024

[TOC]

## Introduction
The repository is dedicated to the Civo Naviagte Conference presentation of [Sveltos](https://github.com/projectsveltos).

## How to

### Assumptions
- We assume a Kubernetes management cluster is already in place with **Sveltos** installed. Sveltos installation details can be found [here](https://projectsveltos.github.io/sveltos/getting_started/install/install/).
- We assume the Kubernetes managed clusters are in place with no CNI in place

### Prerequisites
- Install `kubectl` and `sveltosctl`
- Obtain the `kubeconfig` of the clusters

**Note**: Download the `sveltosctl` [here](https://github.com/projectsveltos/sveltosctl/releases.

### Management Cluster - Create Required Namespaces
```bash
$ kubectl apply -f resources/namespaces/ns_test.yaml,resources/namespaces/ns_prod.yaml
```

### Management Cluster - Register Clusters with Sveltos

```bash
$ ./sveltosctl register cluster \
--namespace=test --cluster=test01 \
--kubeconfig=/path/to/kubeconfig --labels=env=test

$ ./sveltosctl register cluster \
--namespace=prod --cluster=prod01 \
--kubeconfig=/path/to/kubeconfig --labels=env=prod
```

#### Validate Registration

```bash
$ kubectl get sveltosclusters -A --show-labels
```

### Management Cluster - Deploy Cilium

```bash
$ kubectl apply -f resources/test_cluster/clusterprofile_cilium.yaml,resources/prod_cluster/clusterprofile_cilium.yaml
```

#### Validate Deployment

```bash
$ ./sveltosctl show addons
```

### Management Cluster - Deploy Grafana/Prometheus/Loki

```bash
$ kubectl apply -f resources/test_cluster/clusterprofile_grafana_prometheus.yaml,resources/test_cluster/clusterprofile_loki.yaml
$ kubectl apply -f resources/prod_cluster/clusterprofile_grafana_prometheus.yaml,resources/prod_cluster/clusterprofile_loki.yaml
```

#### Validate Deployment

```bash
$ ./sveltosctl show addons
```

### Access Resources

#### Hubble UI
The access to the Cilium Hubble UI, we can either use `kubectl port-forward` or update the `hubble-ui` service to `NodePort` or `LoadBalancer`.

For more details about the Hubble UI, check out the [link](https://docs.cilium.io/en/v1.15/gettingstarted/hubble/).

#### Grafana UI
The Grafana UI is already exposed as a `LoadBalancer` service. The service is located in the `monitoring` namespace.

```
Username: admin
Password: prom-operator
```

#### Loki
To integrate Loki, follow the instructions mentioned [here](https://medium.com/devops-dev/5-step-approach-automate-kubernetes-monitoring-with-projectsveltos-grafana-prometheus-and-loki-696fa7201e5b).