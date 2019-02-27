Dask
===

[Dask](https://dask.org) allows distributed computation in Python.

## Installation

Deploy Kubernetes by following the [Kubernetes GPU Cluster Deployment Guide](kubernetes-cluster.md)

Deploy the [LoadBalancer](ingress.md#on-prem-loadbalancer)

Deploy Dask:

```sh
# Modify chart configuration
vi config/helm/dask.yml

# Deploy
helm install --name dask --values config/helm/dask.yml stable/dask
```

> For more configuration options, see: https://github.com/rmccorm4/charts/tree/update-stable-dask/stable/dask

Upgrading:

```sh
helm upgrade --values config.example/helm/dask.yml dask stable/dask
```
