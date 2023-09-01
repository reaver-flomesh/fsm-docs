---
title: "Integrate Prometheus with FSM"
description: "A simple demo showing how FSM integrates with Prometheus for metrics"
aliases: "/docs/integrations/demo_prometheus"
type: docs
weight: 3
---

## Prometheus and FSM Integration

To familiarize yourself on how FSM works with Prometheus, try installing a new mesh with sample applications to see which metrics are collected.

1. Install FSM with its own Prometheus instance:

   ```console
   fsm install --set fsm.deployPrometheus=true,fsm.enablePermissiveTrafficPolicy=true
   ```

   Wait all pods up.

   ```console
   kubectl wait --for=condition=Ready pod --all -n fsm-system
   ```

1. Create a namespace for sample workloads:

   ```console
   kubectl create namespace metrics-demo
   ```

1. Make the new FSM monitor the new namespace:

   ```console
   fsm namespace add metrics-demo
   ```

1. Configure FSM's Prometheus to scrape metrics from the new namespace:

   ```console
   fsm metrics enable --namespace metrics-demo
   ```

1. Install sample applications:

   ```console
   kubectl apply -f https://raw.githubusercontent.com/flomesh-io/fsm-docs/{{< param fsm_branch >}}/manifests/samples/curl/curl.yaml -n metrics-demo
   kubectl apply -f https://raw.githubusercontent.com/flomesh-io/fsm-docs/{{< param fsm_branch >}}/manifests/samples/httpbin/httpbin.yaml -n metrics-demo
   ```

   Ensure the new Pods are Running and all containers are ready:

   ```console
   kubectl get pods -n metrics-demo
   NAME                       READY   STATUS    RESTARTS   AGE
   curl-54ccc6954c-q8s89      2/2     Running   0          95s
   httpbin-8484bfdd46-vq98x   2/2     Running   0          72s
   ```

1. Generate traffic:

   The following command makes the curl Pod make about 1 request per second to the httpbin Pod forever:

   ```console
   kubectl exec -n metrics-demo -ti "$(kubectl get pod -n metrics-demo -l app=curl -o jsonpath='{.items[0].metadata.name}')" -c curl -- sh -c 'while :; do curl -i httpbin.metrics-demo:14001/status/200; sleep 1; done'

   HTTP/1.1 200 OK
   server: gunicorn/19.9.0
   date: Wed, 06 Jul 2022 02:53:16 GMT
   content-type: text/html; charset=utf-8
   access-control-allow-origin: *
   access-control-allow-credentials: true
   content-length: 0
   connection: keep-alive

   HTTP/1.1 200 OK
   server: gunicorn/19.9.0
   date: Wed, 06 Jul 2022 02:53:17 GMT
   content-type: text/html; charset=utf-8
   access-control-allow-origin: *
   access-control-allow-credentials: true
   content-length: 0
   connection: keep-alive
   ...
   ```

1. View metrics in Prometheus:

   Forward the Prometheus port:

   ```console
   kubectl port-forward -n fsm-system $(kubectl get pods -n fsm-system -l app=fsm-prometheus -o jsonpath='{.items[0].metadata.name}') 7070

   Forwarding from 127.0.0.1:7070 -> 7070
   Forwarding from [::1]:7070 -> 7070
   ```

   Navigate to http://localhost:7070 in a web browser to view the Prometheus UI. The following query shows how many requests per second are being made from the curl pod to the httpbin pod, which should be about 1:

   ```
   irate(sidecar_cluster_upstream_rq_xx{exported_source_workload_name="curl", sidecar_cluster_name="metrics-demo/httpbin|14001"}[30s])
   ```

   Feel free to explore the other metrics available from within the Prometheus UI.

1. Cleanup

   Once you are done with the demo resources, clean them up by first deleting the application namespace:

   ```console
   kubectl delete ns metrics-demo
   ```

   Then, uninstall FSM:

   ```
   fsm uninstall mesh
   Uninstall FSM [mesh name: fsm] ? [y/n]: y
   FSM [mesh name: fsm] uninstalled
   ```

   To remove FSM's cluster wide resources after uninstallation, run the following command. See the [uninstall guide](/guides/operating/uninstall/) for more context and information.

   ```console
   fsm uninstall mesh --delete-namespace -a -f
   ```