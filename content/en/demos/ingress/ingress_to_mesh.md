---
title: "Ingress with Service Mesh"
description: "HTTP ingress implemented by the FSM Ingress controller"
type: docs
weight: 1
draft: false
---

FSM can optionally use the [FSM](https://github.com/flomesh-io/fsm) ingress controller and Pipy-based edge proxies to route external traffic to the Service Mesh backend. This guide demonstrates how to configure HTTP ingress for services managed by the FSM service mesh.

## Prerequisites

- Kubernetes cluster version {{< param min_k8s_version >}} or higher.
- Interact with the API server using `kubectl`.
- FSM CLI installed.
- FSM Ingress Controller installed followed by [installation document](/guides/traffic_management/ingress/kubernetes_ingress/#installation)

## Demo

Assume that we have FSM installed under the `fsm-system` namespace, and named with `fsm`.

```bash
export FSM_NAMESPACE=fsm-system # Replace fsm-system with the namespace where FSM will be installed
export FSM_MESH_NAME=fsm # Replace fsm with the desired FSM mesh name
```

Save the external IP address and port of the entry gateway, which will be used later to test access to the backend application.

```bash
export ingress_host="$(kubectl -n "$FSM_NAMESPACE" get service fsm-ingress -o jsonpath='{.status.loadBalancer.ingress[0].ip}')"
export ingress_port="$(kubectl -n "$FSM_NAMESPACE" get service fsm-ingress -o jsonpath='{.spec.ports[?(@.name=="http")].port}')"
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
kubectl get pods,svc -n httpbin                                                                                                                  default/fsm-system ⎈
NAME                           READY   STATUS            RESTARTS   AGE
pod/httpbin-5c4bbfb664-xsk7j   0/2     PodInitializing   0          29s

NAME              TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)     AGE
service/httpbin   ClusterIP   10.43.83.102   <none>        14001/TCP   30s
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
    http:
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
    namespace: "$FSM_NAMESPACE"
    name: fsm-ingress
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