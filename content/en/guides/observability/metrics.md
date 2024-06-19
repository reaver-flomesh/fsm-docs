---
title: "Metrics"
description: "Proxy and FSM control plane Prometheus metrics"
type: docs
weight: 1
---

FSM generates detailed metrics related to all traffic within the mesh and the FSM control plane. These metrics provide insights into the behavior of applications in the mesh and the mesh itself helping users to troubleshoot, maintain and analyze their applications.

FSM collects metrics directly from the sidecar proxies (Pipy). With these metrics the user can get information about the overall volume of traffic, errors within traffic and the response time for requests.

Additionally, FSM generates metrics for the control plane components. These metrics can be used to monitor the behavior and health of the service mesh.

FSM uses [Prometheus][1] to gather and store consistent traffic metrics and statistics for all applications running in the mesh. Prometheus is an open-source monitoring and alerting toolkit which is commonly used on (but not limited to) Kubernetes and Service Mesh environments.

Each application that is part of the mesh runs in a Pod which contains an Pipy sidecar that exposes metrics (proxy metrics) in the Prometheus format. Furthermore, every Pod that is a part of the mesh and in a namespace with metrics enabled has Prometheus annotations, which makes it possible for the Prometheus server to scrape the application dynamically. This mechanism automatically enables scraping of metrics whenever a pod is added to the mesh.

FSM metrics can be viewed with [Grafana][8] which is an open source visualization and analytics software. It allows you to query, visualize, alert on, and explore your metrics.

Grafana uses Prometheus as backend timeseries database. If Grafana and Prometheus are chosen to be deployed through FSM installation, necessary rules will be set upon deployment for them to interact. Conversely, on a "Bring-Your-Own" or "BYO" model (further explained below), installation of these components will be taken care of by the user.

## Installing Metrics Components

FSM can either provision Prometheus and Grafana instances at install time or FSM can connect to an existing Prometheus and/or Grafana
instance. We call the latter pattern "Bring-Your-Own" or "BYO". The sections below describe how to configure metrics by allowing FSM
to automatically provision the metrics components and with the BYO method.

### Automatic Provisioning

By default, both Prometheus and Grafana are disabled.

However, when configured with the `--set=fsm.deployPrometheus=true` flag, FSM installation will deploy a Prometheus instance to scrape the sidecar's metrics endpoints. Based on the metrics scraping configuration set by the user, FSM will annotate pods part of the mesh with necessary metrics annotations to have Prometheus reach and scrape the pods to collect relevant metrics. The [scraping configuration file](https://github.com/flomesh-io/fsm/blob/{{< param fsm_branch >}}/charts/fsm/templates/prometheus-configmap.yaml) defines the default Prometheus behavior and the set of metrics collected by FSM.

To install Grafana for metrics visualization, pass the `--set=fsm.deployGrafana=true` flag to the `fsm install` command. FSM provides a pre-configured dashboard that is documented in [FSM Grafana dashboards](#fsm-grafana-dashboards).

```bash
 fsm install --set=fsm.deployPrometheus=true \
             --set=fsm.deployGrafana=true
```

> Note: The Prometheus and Grafana instances deployed automatically by FSM have simple configurations that do not include high availability, persistent storage, or locked down security. If production-grade instances are required, pre-provision them and follow the BYO instructions on this page to integrate them with FSM.

### Bring-Your-Own

#### Prometheus

The following section documents the additional steps needed to allow an already running Prometheus instance to poll the endpoints of an FSM mesh.

##### List of Prerequisites for BYO Prometheus

- Already running an accessible Prometheus instance _outside_ of the mesh.
- A running FSM control plane instance, deployed without metrics stack.
- We will assume having Grafana reach Prometheus, exposing or forwarding Prometheus or Grafana web ports and configuring Prometheus to reach Kubernetes API services is taken care of or otherwise out of the scope of these steps.

##### Configuration

- Make sure the Prometheus instance has appropriate RBAC rules to be able to reach both the pods and Kubernetes API - this might be dependent on specific requirements and situations for different deployments:
```yaml
- apiGroups: [""]
  resources: ["nodes", "nodes/proxy",  "nodes/metrics", "services", "endpoints", "pods", "ingresses", "configmaps"]
  verbs: ["list", "get", "watch"]
- apiGroups: ["extensions"]
  resources: ["ingresses", "ingresses/status"]
  verbs: ["list", "get", "watch"]
- nonResourceURLs: ["/metrics"]
  verbs: ["get"]
```

- If desired, use the Prometheus Service definition to allow Prometheus to scrape itself:
```yaml
annotations:
  prometheus.io/scrape: "true"
  prometheus.io/port: "<API port for prometheus>" # Depends on deployment - FSM automatic deployment uses 7070 by default, controlled by `values.yaml`
```

- Amend Prometheus' configmap to reach the pods/Pipy endpoints. FSM automatically appends the port annotations to the pods and takes care of pushing the listener configuration to the pods for Prometheus to reach:
```yaml
- job_name: 'kubernetes-pods'
  kubernetes_sd_configs:
  - role: pod
  relabel_configs:
  - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_scrape]
    action: keep
    regex: true
  - source_labels: [__meta_kubernetes_pod_annotation_prometheus_io_path]
    action: replace
    target_label: __metrics_path__
    regex: (.+)
  - source_labels: [__address__, __meta_kubernetes_pod_annotation_prometheus_io_port]
    action: replace
    regex: ([^:]+)(?::\d+)?;(\d+)
    replacement: $1:$2
    target_label: __address__
  - source_labels: [__meta_kubernetes_namespace]
    action: replace
    target_label: source_namespace
  - source_labels: [__meta_kubernetes_pod_name]
    action: replace
    target_label: source_pod_name
  - regex: '(__meta_kubernetes_pod_label_app)'
    action: labelmap
    replacement: source_service
  - regex: '(__meta_kubernetes_pod_label_fsm_sidecar_uid|__meta_kubernetes_pod_label_pod_template_hash|__meta_kubernetes_pod_label_version)'
    action: drop
  - source_labels: [__meta_kubernetes_pod_controller_kind]
    action: replace
    target_label: source_workload_kind
  - source_labels: [__meta_kubernetes_pod_controller_name]
    action: replace
    target_label: source_workload_name
  - source_labels: [__meta_kubernetes_pod_controller_kind]
    action: replace
    regex: ^ReplicaSet$
    target_label: source_workload_kind
    replacement: Deployment
  - source_labels:
    - __meta_kubernetes_pod_controller_kind
    - __meta_kubernetes_pod_controller_name
    action: replace
    regex: ^ReplicaSet;(.*)-[^-]+$
    target_label: source_workload_name
```

#### Grafana

The following section assumes a Prometheus instance has already been configured as a data source for a running Grafana instance. Refer to the [Prometheus and Grafana](/demos/prometheus_grafana) demo for an example on how to create and configure a Grafana instance.

##### Importing FSM Dashboards

FSM Dashboards are available through [our repository](https://github.com/flomesh-io/fsm/tree/{{< param fsm_branch >}}/charts/fsm/grafana/pipy/dashboards), which can be imported as json blobs on the web admin portal.

Detailed instructions for importing FSM dashboards can be found in the [Prometheus and Grafana](/demos/prometheus_grafana) demo. Refer to [FSM Grafana dashboard](#fsm-grafana-dashboards) for an overview of the pre-configured dashboards.

## Metrics scraping

Metrics scraping can be configured using the `fsm metrics` command. By default, FSM **does not** configure metrics scraping for pods in the mesh. Metrics scraping can be enabled or disabled at namespace scope such that pods belonging to configured namespaces can be enabled or disabled for scraping metrics.

For metrics to be scraped, the following prerequisites must be met:

- The namespace must be a part of the mesh, ie. it must be labeled with the `flomesh.io/monitored-by` label with an appropriate mesh name. This can be done using the `fsm namespace add` command.
- A running service able to scrape Prometheus endpoints. FSM provides configuration for an [automatic bringup of Prometheus](#automatic-provisioning); alternatively users can [bring their own Prometheus](#prometheus).

To enable one or more namespaces for metrics scraping:

```bash
fsm metrics enable --namespace test
fsm metrics enable --namespace "test1, test2"

```

To disable one or more namespaces for metrics scraping:

```bash
fsm metrics disable --namespace test
fsm metrics disable --namespace "test1, test2"
```

Enabling metrics scraping on a namespace also causes the fsm-injector to add the following annotations to pods in that namespace:

```yaml
prometheus.io/scrape: true
prometheus.io/port: 15010
prometheus.io/path: /stats/prometheus
```

## Available Metrics

FSM exports metrics about the traffic within the mesh as well as metrics about the control plane.

### Custom Pipy Metrics

To implement the [SMI Metrics Specification][7], the Pipy proxy in FSM generates the following statistics for HTTP traffic

`fsm_request_total`: a counter metric that is self-incrementing with each proxy request. By querying this metric, you can see the success and failure rates of requests for the services in the mesh.

`fsm_request_duration_ms`: A histogram metric that indicates the duration of a proxy request in milliseconds. This metric is queried to understand the latency between services in the mesh.

Both metrics have the following labels.

`source_kind`: the Kubernetes resource type of the workload that generated the request, e.g. `Deployment`, `DaemonSet`, etc.

`destination_kind`: The Kubernetes resource type that processes the requested workload, e.g. `Deployment`, `DaemonSet`, etc.

`source_name`: The name of the Kubernetes that generated the requested workload.

`destination_name`: The name of the Kubernetes that processed the requested workload.

`source_pod`: the name of the pod in Kubernetes that generated the request.

`destination_pod`: the name of the pod that processed the request in Kubernetes.

`source_namespace`: the namespace in Kubernetes of the workload that generated the request.

`destination_namespace`: the namespace in Kubernetes of the workload that processed the request.

In addition, the `fsm_request_total` metric has a `response_code` tag that indicates the HTTP status code of the request, e.g. `200`, `404`, etc.

### Control Plane

The following metrics are exposed in the Prometheus format by the FSM control plane components. The `fsm-controller` and `fsm-injector` pods have the following Prometheus annotation.

```yaml
annotations:
   prometheus.io/scrape: 'true'
   prometheus.io/port: '9091'
```
| Metric                                | Type      | Labels                                  | Description                                                                      |
| ------------------------------------- | --------- | --------------------------------------- | -------------------------------------------------------------------------------- |
| fsm_k8s_api_event_count               | Count     | type, namespace                         | Number of events received from the Kubernetes API Server                         |
| fsm_proxy_connect_count               | Gauge     |                                         | Number of proxies connected to FSM controller                                    |
| fsm_proxy_reconnect_count             | Count     |                                         | IngressGateway defines the certificate specification for an ingress gateway      |
| fsm_proxy_response_send_success_count | Count     | proxy_uuid, identity, type              | Number of responses successfully sent to proxies                                 |
| fsm_proxy_response_send_error_count   | Count     | proxy_uuid, identity, type              | Number of responses that errored when being set to proxies                       |
| fsm_proxy_config_update_time          | Histogram | resource_type, success                  | Histogram to track time spent for proxy configuration                            |
| fsm_proxy_broadcast_event_count       | Count     |                                         | Number of ProxyBroadcast events published by the FSM controller                  |
| fsm_proxy_xds_request_count           | Count     | proxy_uuid, identity, type              | Number of XDS requests made by proxies                                           |
| fsm_proxy_max_connections_rejected    | Count     |                                         | Number of proxy connections rejected due to the configured max connections limit |
| fsm_cert_issued_count                 | Count     |                                         | Total number of XDS certificates issued to proxies                               |
| fsm_cert_issued_time                  | Histogram |                                         | Histogram to track time spent to issue xds certificate                           |
| fsm_admission_webhook_response_total  | Count     | kind, success                           | Total number of admission webhook responses generated                            |
| fsm_error_err_code_count              | Count     | err_code                                | Number of errcodes generated by FSM                                              |
| fsm_http_response_total               | Count     | code, method, path                      | Number of HTTP responses sent                                                    |
| fsm_http_response_duration            | Histogram | code, method, path                      | Duration in seconds of HTTP responses sent                                       |
| fsm_feature_flag_enabled              | Gauge     | feature_flag                            | Represents whether a feature flag is enabled (1) or disabled (0)                 |
| fsm_conversion_webhook_resource_total | Count     | kind, success, from_version, to_version | Number of resources converted by conversion webhooks                             |
| fsm_events_queued                     | Gauge     |                                         | Number of events seen but not yet processed by the control plane                 |
| fsm_reconciliation_total              | Count     | kind                                    | Counter of resource reconciliations invoked                                      |

#### Error Code Metrics

When an error occurs in the FSM control plane the ErrCodeCounter Prometheus metric is incremented for the related FSM error code. For the complete list of error codes and their descriptions, see [FSM Control Plane Error Code Troubleshooting Guide](/guides/troubleshooting/control_plane_error_codes).

The fully-qualified name of the error code metric is `fsm_error_err_code_count`.

> Note: Metrics corresponding to errors that result in process restarts might not be scraped in time.

## Query metrics from Prometheus

### Before you begin

Ensure that you have followed the steps to run [FSM Demo][2]

### Querying proxy metrics for request count

1. Verify that the Prometheus service is running in your cluster
   - In kubernetes, execute the following command: `kubectl get svc fsm-prometheus -n <fsm-namespace>`.
     ![image](https://user-images.githubusercontent.com/59101963/85906800-478b3580-b7c4-11ea-8eb2-63bd83647e5f.png)
   - Note: `<fsm-namespace>` refers to the namespace where the fsm control plane is installed.
2. Open up the Prometheus UI
   - Ensure you are in root of the repository and execute the following script: `./scripts/port-forward-prometheus.sh`
   - Visit the following url [http://localhost:7070][5] in your web browser
3. Execute a Prometheus query
   - In the "Expression" input box at the top of the web page, enter the text: `sidecar_cluster_upstream_rq_xx{sidecar_response_code_class="2"}` and click the execute button
   - This query will return the successful http requests

Sample result will be:
![image](https://user-images.githubusercontent.com/59101963/85906690-f24f2400-b7c3-11ea-89b2-a3c42041c7a0.png)

## Visualize metrics with Grafana

### List of Prerequisites for Viewing Grafana Dashboards

Ensure that you have followed the steps to run [FSM Demo][2]

### Viewing a Grafana dashboard for service to service metrics

1. Verify that the Prometheus service is running in your cluster
   - In kubernetes, execute the following command: `kubectl get svc fsm-prometheus -n <fsm-namespace>`
     ![image](https://user-images.githubusercontent.com/59101963/85906800-478b3580-b7c4-11ea-8eb2-63bd83647e5f.png)
2. Verify that the Grafana service is running in your cluster
   - In kubernetes, execute the following command: `kubectl get svc fsm-grafana -n <fsm-namespace>`
     ![image](https://user-images.githubusercontent.com/59101963/85906847-70abc600-b7c4-11ea-853d-f4c9b188ab9f.png)
3. Open up the Grafana UI
   - Ensure you are in root of the repository and execute the following script: `./scripts/port-forward-grafana.sh`
   - Visit the following url [http://localhost:3000][4] in your web browser
4. The Grafana UI will request for login details, use the following default settings:
   - username: admin
   - password: admin
5. Viewing Grafana dashboard for service to service metrics
   - From the Grafana's dashboards left hand corner navigation menu you can navigate to the FSM Service to Service Dashboard in the folder FSM Data Plane
   - Or visit the following url [http://localhost:3000/d/FSMs2sMetrics/fsm-service-to-service-metrics?orgId=1][6] in your web browser

FSM Service to Service Metrics dashboard will look like:
![image](https://user-images.githubusercontent.com/59101963/85907233-a604e380-b7c5-11ea-95b5-9190fbc7967f.png)

## FSM Grafana dashboards

FSM provides some pre-cooked Grafana dashboards to display and track services related information captured by Prometheus:

1. FSM Data Plane
   - **FSM Data Plane Performance Metrics**: This dashboard lets you view the performance of FSM's data plane
     ![image](https://user-images.githubusercontent.com/64559656/138173256-28011b16-cace-4365-b166-db909543472e.png)
   - **FSM Service to Service Metrics**: This dashboard lets you view the traffic metrics from a given source service to a given destination service
     ![image](https://user-images.githubusercontent.com/64559656/141853912-10ec3767-3d5b-40e8-8f13-d39a32980183.png)
   - **FSM Pod to Service Metrics**: This dashboard lets you investigate the traffic metrics from a pod to all the services it connects/talks to
     ![image](https://user-images.githubusercontent.com/64559656/140724337-0568dde0-e6c5-4764-8b6f-c1fcaf144b4e.png)
   - **FSM Workload to Service Metrics**: This dashboard provides the traffic metrics from a workload (deployment, replicaSet) to all the services it connects/talks to
     ![image](https://user-images.githubusercontent.com/64559656/140724800-8152cb8b-1617-4866-b008-f12c31f702c2.png)
   - **FSM Workload to Workload Metrics**: This dashboard displays the latencies of requests in the mesh from workload to workload
     ![image](https://user-images.githubusercontent.com/64559656/140718968-b3999e30-e6d1-4d95-b07b-0043595aca71.png)

2. FSM Control Plane
   - **FSM Control Plane Metrics**: This dashboard provides traffic metrics from the given service to FSM's control plane
     ![image](https://user-images.githubusercontent.com/64559656/138173115-0a012450-0d91-449d-9c09-975b68fde03d.png)
   - **Mesh and Pipy Details**: This dashboard lets you view the performance and behavior of FSM's control plane
     ![image](https://user-images.githubusercontent.com/64559656/141852750-61da99ac-a431-4251-bd97-8aa4601232c3.png)

[1]: https://prometheus.io/docs/introduction/overview/
[2]: https://github.com/flomesh-io/fsm/blob/{{< param fsm_branch >}}/demo/README.md
[3]: https://grafana.com/docs/grafana/latest/getting-started/#what-is-grafana
[4]: http://localhost:3000
[5]: http://localhost:7070
[6]: http://localhost:3000/d/FSMs2sMetrics/fsm-service-to-service-metrics?orgId=1
[7]: https://github.com/servicemeshinterface/smi-spec/blob/master/apis/traffic-metrics/v1alpha1/traffic-metrics.md
[8]: https://grafana.com/oss/grafana/
