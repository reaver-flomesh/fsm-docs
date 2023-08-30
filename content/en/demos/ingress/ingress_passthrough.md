---
title: "FSM Ingress Controller - SSL Passthrough"
description: "How to use SSL passthrough feature of FSM Ingress"
type: docs
weight: 13
---

This guide demonstrate how to configure SSL passthrough feature of FSM Ingress

### Prerequisites

- Kubernetes cluster version {{< param min_k8s_version >}} or higher.
- Interact with the API server using `kubectl`.
- FSM CLI installed.
- FSM Ingress Controller installed followed by [installation document](/guides/traffic_management/ingress/kubernetes_ingress/#installation)

## Setup

Once environment preparation done, let's retrieve Ingress host IP and port information.

```bash
export FSM_NAMESPACE=fsm-system #change this to the namespace your FSM ingress installed in

export ingress_host="$(kubectl -n "$FSM_NAMESPACE" get service fsm-ingress -o jsonpath='{.status.loadBalancer.ingress[0].ip}')"
export ingress_port="$(kubectl -n "$FSM_NAMESPACE" get service fsm-ingress -o jsonpath='{.spec.ports[?(@.name=="http")].port}')"
```

## Test

For simplicity, we will not deploy an upstream service here, but instead use `https://httpbin.org` directly as the upstream, and `resolve` it to the ingress address obtained above through the `curl`'s revolve parameter. If the port of ingress is not `433`, you can use the `connect-to` parameter -`-connect-to httpbin.org:443:$ingress_host:$ingress_port`.

```shell
curl https://httpbin.org/get -i --resolve httpbin.org:443:$ingress_host:$ingress_port

HTTP/2 200
date: Tue, 31 Jan 2023 11:21:41 GMT
content-type: application/json
content-length: 255
server: gunicorn/19.9.0
access-control-allow-origin: *
access-control-allow-credentials: true

{
  "args": {},
  "headers": {
    "Accept": "*/*",
    "Host": "httpbin.org",
    "User-Agent": "curl/7.68.0",
    "X-Amzn-Trace-Id": "Root=1-63d8f9c5-5af02436470161040dc68f1e"
  },
  "origin": "20.205.11.203",
  "url": "https://httpbin.org/get"
}
```