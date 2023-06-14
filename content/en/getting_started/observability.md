---
title: "Configure Observability with Prometheus and Grafana"
description: "Use FSM's observability integrations with Prometheus and Grafana to inspect the traffic between the bookstore applications"
type: docs
weight: 5
---

The following article shows you how to install FSM with automatic provisioning of the Prometheus and Grafana stack for observability and monitoring. For an example using a bring your own (BYO) Prometheus and Grafana stack on your cluster with FSM, see the [Integrate FSM with Prometheus and Grafana](/docs/demos/prometheus_grafana/) demo.

The configuration created in this article should not be used in production environments. For production-grade deployments, see [Prometheus Operator](https://github.com/prometheus-operator/prometheus-operator/blob/master/Documentation/user-guides/getting-started.md) and [Deploy Grafana in Kubernetes](https://grafana.com/docs/grafana/latest/installation/kubernetes/).


## Install FSM with Prometheus and Grafana

On `fsm install`, a Prometheus and/or Grafana instance can be automatically provisioned with the default FSM configuration.
```bash
 fsm install --set=fsm.deployPrometheus=true \
             --set=fsm.deployGrafana=true
```
More information on observability can be found in the [Observability Guide](/docs/guides/observability).

## Prometheus

When configured with the `--set=fsm.deployPrometheus=true` flag, FSM installation will deploy a Prometheus instance to scrape the sidecar and FSM control plane's metrics endpoints. The [scraping configuration file](https://github.com/flomesh-io/fsm/blob/{{< param fsm_branch >}}/charts/fsm/templates/prometheus-configmap.yaml) defines the default Prometheus behavior and the set of metrics collected by FSM.

## Grafana

FSM can be configured to deploy a [Grafana](https://grafana.com/grafana/) instance using the `--set=fsm.deployGrafana=true` flag in `fsm install`. FSM provides pre-configured dashboards that are documented in the [FSM Grafana dashboards](/docs/guides/observability/metrics/#fsm-grafana-dashboards) section of the Observability Guide.

## Enable Metrics Scraping

Metrics can be enabled at the namespace scope using the `fsm metrics` command. By default, FSM **does not** configure metrics scraping for pods in the mesh. 
```bash
fsm metrics enable --namespace test
fsm metrics enable --namespace "test1, test2"

```
> Note: The namespace that you are enabling for metrics scraping must already be a part of the mesh.

## Inspect Dashboards

The FSM Grafana dashboards can be viewed with the following command:

```bash
fsm dashboard
```

Navigate to http://localhost:3000 to access the Grafana dashboards. The default user name is `admin` and the default password is `admin`. On the Grafana homepage click on the **Home** icon, you will see a folder containing dashboards for both FSM Control Plane and FSM Data Plane.

## Next Steps

[Cleanup sample applications and uninstall FSM](/docs/getting_started/cleanup/).