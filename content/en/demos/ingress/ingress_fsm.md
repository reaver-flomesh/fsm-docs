---
title: "Ingress with FSM"
description: "HTTP ingress implemented by the FSM Ingress controller"
type: docs
weight: 10
draft: false
---

FSM can optionally use the [FSM](https://github.com/flomesh-io/fsm) ingress controller and Pipy-based edge proxies to route external traffic to the Service Mesh backend. This guide demonstrates how to configure HTTP ingress for services managed by the FSM service mesh.

## Prerequisites

- Kubernetes cluster version {{< param min_k8s_version >}} or higher.
- Interact with the API server using `kubectl`.
- FSM is not installed, and must be removed if it is installed.
- Installed `fsm` or `Helm 3` command line tool for installing FSM and FSM.
- FSM version >= v1.1.0.

## Demo

First, install FSM and fsm under the `fsm-system` namespace, and name the grid `fsm`.

```bash
export fsm_namespace=fsm-system # Replace fsm-system with the namespace where FSM will be installed
export fsm_mesh_name=fsm # Replace fsm with the desired FSM mesh name
```

Using the `fsm` command line tool.

```bash
fsm install --set fsm.enabled=true \
    --mesh-name "$fsm_mesh_name" \
    --fsm-namespace "$fsm_namespace"
```

Using ``Helm`` to install.

```bash
helm install "$fsm_mesh_name" fsm --repo https://flomesh-io.github.io/fsm \
    --set fsm.enabled=true
```

In order to authorize clients by restricting access to backend traffic, we will configure IngressBackend so that only ingress traffic from the `ingress-pipy-controller` endpoint can be routed to the backend service. In order to discover the `ingress-pipy-controller` endpoint, we need the FSM controller and the corresponding namespace to monitor it. However, to ensure that the FSM functions properly, it cannot be injected with a Pipy sidecar.

```bash
kubectl label namespace "$fsm_namespace" flomesh.io/monitored-by="$fsm_mesh_name"
```

Save the external IP address and port of the entry gateway, which will be used later to test access to the backend application.

```bash
export ingress_host="$(kubectl -n "$fsm_namespace" get service ingress-pipy-controller -o jsonpath='{.status.loadBalancer.ingress[0].ip}') "
export ingress_port="$(kubectl -n "$fsm_namespace" get service ingress-pipy-controller -o jsonpath='{.spec.ports[? (@.name=="http")].port}')"
```

The next step is to deploy the sample `httpbin` service.

```bash
# Create a namespace
kubectl create ns httpbin

# Add the namespace to the mesh
fsm namespace add httpbin

# Deploy the application
kubectl apply -f https://raw.githubusercontent.com/flomesh-io/fsm-docs/{{< param fsm_branch >}}/manifests/samples/httpbin/httpbin.yaml -n httpbin
```

Ensure that the `httpbin` service and pod are up and running properly by

```console
kubectl get pods -n httpbin
NAME READY STATUS RESTARTS AGE
httpbin-74677b7df7-zzlm2 2/2 Running 0 11h

kubectl get svc -n httpbin
NAME TYPE CLUSTER-IP EXTERNAL-IP PORT(S) AGE
httpbin ClusterIP 10.0.22.196 <none> 14001/TCP 11h
```

### HTTP Ingress

Next, create the necessary HTTPProxy and IngressBackend configurations to allow external clients to access port `14001` of the `httpbin` service under the `httpbin` namespace. Because TLS is not used, the link from the fsm entry gateway to the `httpbin` backend pod is not encrypted.

```bash
kubectl apply -f - <<EOF
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: httpbin
  namespace: httpbin
spec:
  ingressClassName: pipy
  rules:
  - host: httpbin.org
    paths: http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: httpbin
            port:
              number: 14001
---
kind: IngressBackend
apiVersion: policy.flomesh.io/v1alpha1
metadata:
  name: httpbin
  namespace: httpbin
spec:
  backends:
  - name: httpbin
    port:
      number: 14001 # targetPort of httpbin service
      protocol: http
  sources:
  - kind: Service
    namespace: "$fsm_namespace"
    name: ingress-pipy-controller
EOF
```

Now we expect external clients to have access to the `httpbin` service, with the `HOST` request header of the HTTP request being `httpbin.org`.

```console
curl -sI http://"$ingress_host":"$ingress_port"/get -H "Host: httpbin.org"
HTTP/1.1 200 OK
server: gunicorn/19.9.0
date: Tue, 05 Jul 2022 07:34:11 GMT
content-type: application/json
content-length: 241
access-control-allow-origin: *
access-control-allow-credentials: true
connection: keep-alive
```