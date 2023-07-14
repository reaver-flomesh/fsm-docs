---
title: "ErieCanal Ingress Controller - SSL Passthrough"
description: "How to use SSL passthrough feature of ErieCanal Ingress"
type: docs
weight: 13
---

This guide demonstrate how to configure SSL passthrough feature of ErieCanal Ingress

### Prerequisites

* Kubernetes cluster, version {{< param min_k8s_version >}} and higher
* Helm 3 CLI for standalone installation of ErieCanal Ingress


### Install ErieCanal  Ingress

if you haven't yet installed ErieCanal, you can use Helm to install ec.

```shell
helm repo add ec https://ec.flomesh.io
helm repo update

helm install \
  --namespace erie-canal \
  --create-namespace \
  --set ec.ingress.tls.sslPassthrough.enabled=true \
  --set ec.serviceLB.enabled=true \
  --set ec.ingress.tls.enabled=true \
  ec ec/erie-canal
```

Make sure pods are up and running.

```shell
kubectl get po -n erie-canal
NAME                                                      READY   STATUS    RESTARTS   AGE
erie-canal-repo-5f6dc647c7-w45sl                          1/1     Running   0          26s
erie-canal-manager-864c74b978-fdks9                       1/1     Running   0          26s
svclb-erie-canal-ingress-pipy-controller-26382000-scgt2   2/2     Running   0          19s
erie-canal-ingress-pipy-5cfc98bb48-v9x5s                  1/1     Running   0          26s
```

Retrieve Ingress host IP and port information.

```shell
export ingress_host="$(kubectl -n erie-canal get service erie-canal-ingress-pipy-controller -o jsonpath='{.status.loadBalancer.ingress[0].ip}')"
export ingress_port="$(kubectl -n erie-canal get service erie-canal-ingress-pipy-controller -o jsonpath='{.spec.ports[?(@.name=="https")].port}')"
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