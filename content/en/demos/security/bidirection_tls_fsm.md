---
title: "Bi-direction TLS with FSM Ingress"
description: "Configuring different TLS certificates for Ingress and Egress"
type: docs
weight: 12
---

This guide will demonstrate with multiple scenarios on how to configure different TLS certificates for Flomesh Service Mesh (FSM) Ingress and Egress communication.

## Prerequisites

- Kubernetes cluster running Kubernetes {{< param min_k8s_version >}} or greater.
- Have FSM installed.
- Have `kubectl` available to interact with the API server.
- Have `fsm` CLI available for managing the service mesh.
- Have FSM Ingress Controller installed.

### Install FSM Ingress Controller

if you haven't yet installed FSM Ingress controller, you can install that quickly via

```bash
fsm install \
    --set=fsm.fsmIngress.enabled=true \
    --set=fsm.fsmIngress.tls.enabled=true \
    --set=fsm.fsmIngress.tls.mTLS=true

kubectl wait --namespace fsm-system \
  --for=condition=ready pod \
  --selector=app=fsm-ingress \
  --timeout=300s
```

### Deploy demo pods

```bash
#Sample server service
kubectl create namespace egress-server
kubectl apply -n egress-server -f https://raw.githubusercontent.com/flomesh-io/fsm-docs/{{< param fsm_branch >}}/manifests/samples/bidir-mtls/server.yaml

#Sample middle-ware service
kubectl create namespace egress-middle
fsm namespace add egress-middle
kubectl apply -n egress-middle -f https://raw.githubusercontent.com/flomesh-io/fsm-docs/{{< param fsm_branch >}}/manifests/samples/bidir-mtls/middle.yaml

#Sample client
kubectl create namespace egress-client
kubectl apply -n egress-client -f https://raw.githubusercontent.com/flomesh-io/fsm-docs/{{< param fsm_branch >}}/manifests/samples/bidir-mtls/client.yaml

#Wait for POD to start properly
kubectl wait --for=condition=ready pod -n egress-server -l app=server --timeout=180s
kubectl wait --for=condition=ready pod -n egress-middle -l app=middle --timeout=180s
kubectl wait --for=condition=ready pod -n egress-client -l app=client --timeout=180s
```
### Scenario#1: Client HTTP & HTTP Ingress & mTLS Egress

#### Test commands

Traffic flow:

Client --**http**--> ingress-pipy Controller

```bash
kubectl exec "$(kubectl get pod -n egress-client -l app=client -o jsonpath='{.items..metadata.name}')" -n egress-client -- curl -si http://fsm-ingress.fsm-system/hello
```

#### Test results

The correct return result is similar to :

```bash
HTTP/1.1 404 Not Found
Server: pipy/0.70.0
content-length: 17
connection: keep-alive

Service Not Found
```

#### Setup Ingress Rules

```bash
kubectl apply -f - <<EOF
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: egress-middle
  namespace: egress-middle
spec:
  ingressClassName: pipy
  rules:
  - http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: middle
            port:
              number: 8080
EOF
```

#### Setup IngressBackend

```bash
kubectl apply -f - <<EOF
kind: IngressBackend
apiVersion: policy.flomesh.io/v1alpha1
metadata:
  name: egress-middle
  namespace: egress-middle
spec:
  backends:
  - name: middle
    port:
      number: 8080 # targetPort of middle service
      protocol: http
  sources:
  - kind: Service
    namespace: fsm-system
    name: fsm-ingress
EOF
```

#### Test Commands

Traffic Flow:

Client --**http**--> FSM Ingress --**http** --> sidecar --> Middle

```bash
kubectl exec "$(kubectl get pod -n egress-client -l app=client -o jsonpath='{.items..metadata.name}')" -n egress-client -- curl -si http://fsm-ingress.fsm-system/hello
```

#### Test Results

The correct return result is similar to :

```bash
HTTP/1.1 200 OK
date: Sun, 04 Dec 2022 12:03:47 GMT
content-type: text/plain; charset=utf-8
fsm-stats-namespace: egress-middle
fsm-stats-kind: Deployment
fsm-stats-name: middle
fsm-stats-pod: middle-58d9865569-dwcvf
content-length: 13
connection: keep-alive

hello world.
```

#### Disable Egress Permissive mode

```bash
export FSM_NAMESPACE=fsm-system
kubectl patch meshconfig fsm-mesh-config -n "$FSM_NAMESPACE" -p '{"spec":{"traffic":{"enableEgress":false}}}' --type=merge
```

#### Enable Egress Policy

```bash
export FSM_NAMESPACE=fsm-system
kubectl patch meshconfig fsm-mesh-config -n "$FSM_NAMESPACE" -p '{"spec":{"featureFlags":{"enableEgressPolicy":true}}}' --type=merge
```

#### Create Egress mTLS Secret

```bash
curl -s https://raw.githubusercontent.com/flomesh-io/fsm-docs/{{< param fsm_branch >}}/manifests/samples/bidir-mtls/certs/pipy-ca.crt -o pipy-ca.crt
curl -s https://raw.githubusercontent.com/flomesh-io/fsm-docs/{{< param fsm_branch >}}/manifests/samples/bidir-mtls/certs/middle.crt -o middle.crt
curl -s https://raw.githubusercontent.com/flomesh-io/fsm-docs/{{< param fsm_branch >}}/manifests/samples/bidir-mtls/certs/middle.key -o middle.key

kubectl create secret generic -n fsm-system egress-middle-cert \
  --from-file=ca.crt=./pipy-ca.crt \
  --from-file=tls.crt=./middle.crt \
  --from-file=tls.key=./middle.key 
```

#### Setup Egress Policy

```bash
kubectl apply -f - <<EOF
kind: Egress
apiVersion: policy.flomesh.io/v1alpha1
metadata:
  name: server-8443
  namespace: egress-middle
spec:
  sources:
  - kind: ServiceAccount
    name: middle
    namespace: egress-middle
    mtls:
      issuer: other
      cert:
        sn: 1
        subjectAltNames: 
        - flomesh.io
        expiration: 2030-1-1 00:00:00
        secret:
          name: egress-middle-cert
          namespace: fsm-system
  hosts:
  - server.egress-server.svc.cluster.local
  ports:
  - number: 8443
    protocol: http
EOF
```

#### Test Commands

Traffic Flow:

Client --**http**--> FSM Ingress --**http**--> sidecar --> Middle --> sidecar --**egress mtls**--> Server

```bash
kubectl exec "$(kubectl get pod -n egress-client -l app=client -o jsonpath='{.items..metadata.name}')" -n egress-client -- curl -si http://fsm-ingress.fsm-system/time
```

#### Test Results

The correct return result is similar to :

```bash
HTTP/1.1 200 OK
date: Sun, 04 Dec 2022 12:08:14 GMT
content-type: text/plain; charset=utf-8
fsm-stats-namespace: egress-middle
fsm-stats-kind: Deployment
fsm-stats-name: middle
fsm-stats-pod: middle-58d9865569-dwcvf
content-length: 76
connection: keep-alive

The current time: 2022-12-04 12:08:14.034663797 +0000 UTC m=+1093.291560087
```

This business scenario is tested and the strategy is cleaned up to avoid affecting subsequent tests

```bash
kubectl delete ingress -n egress-middle egress-middle
kubectl delete ingressbackend -n egress-middle egress-middle
kubectl delete egress -n egress-middle server-8443
kubectl delete secrets -n fsm-system egress-middle-cert
```

### Scenario#2: HTTP FSM & mTLS Ingress & mTLS Egress

#### Test Commands

Traffic flow:

Client --**http**--> FSM Ingress Controller

```bash
kubectl exec "$(kubectl get pod -n egress-client -l app=client -o jsonpath='{.items..metadata.name}')" -n egress-client -- curl -si http://fsm-ingress.fsm-system/hello
```

#### Test Results

The correct return result is similar to :

```bash
HTTP/1.1 404 Not Found
Server: pipy/0.70.0
content-length: 17
connection: keep-alive

Service Not Found
```

#### Setup Ingress Controller TLS Certificate

```bash
export FSM_NAMESPACE=fsm-system
kubectl patch meshconfig fsm-mesh-config -n "$FSM_NAMESPACE" -p \
'{
  "spec":{
    "certificate":{
      "ingressGateway":{
        "secret":{
          "name":"ingress-controller-cert",
          "namespace":"fsm-system"
        },
        "subjectAltNames":["fsm.fsm-system.cluster.local"],
        "validityDuration":"24h"
      }
    }
  }
}' \
--type=merge
```
> Note: The Subject Alternative Name (SAN) is of the form <service-account>.<namespace>.cluster.local, where the service account and namespace correspond to the ingress-pipy service.

#### Setup Ingress Rules

```bash
kubectl apply -f - <<EOF
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: egress-middle
  namespace: egress-middle
  annotations:
    # upstream-ssl-name for a service is of the form <service-account>.<namespace>.cluster.local
    pipy.ingress.kubernetes.io/upstream-ssl-name: "middle.egress-middle.cluster.local"
    pipy.ingress.kubernetes.io/upstream-ssl-secret: "fsm-system/ingress-controller-cert"
    pipy.ingress.kubernetes.io/upstream-ssl-verify: "on"
spec:
  ingressClassName: pipy
  rules:
  - http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: middle
            port:
              number: 8080
EOF
```

#### Setup IngressBackend Policy

```bash
kubectl apply -f - <<EOF
kind: IngressBackend
apiVersion: policy.flomesh.io/v1alpha1
metadata:
  name: egress-middle
  namespace: egress-middle
spec:
  backends:
  - name: middle
    port:
      number: 8080 # targetPort of middle service
      protocol: https
    tls:
      skipClientCertValidation: false
  sources:
  - kind: Service
    namespace: fsm-system
    name: fsm-ingress
  - kind: AuthenticatedPrincipal
    name: fsm.fsm-system.cluster.local
EOF
```

#### Test Commands

Traffic flow:

Client --**http**--> FSM Ingress --**mtls** --> sidecar --> Middle

```bash
kubectl exec "$(kubectl get pod -n egress-client -l app=client -o jsonpath='{.items..metadata.name}')" -n egress-client -- curl -si http://fsm-ingress.fsm-system/hello
```

#### Test Results

The correct return result is similar to :

```bash
HTTP/1.1 200 OK
date: Fri, 09 Dec 2022 08:00:36 GMT
content-type: text/plain; charset=utf-8
fsm-stats-namespace: egress-middle
fsm-stats-kind: Deployment
fsm-stats-name: middle
fsm-stats-pod: middle-7956998bd5-bm5vx
content-length: 13
connection: keep-alive

hello world.
```

#### Disable Egress Permissive mode

```bash
export FSM_NAMESPACE=fsm-system
kubectl patch meshconfig fsm-mesh-config -n "$FSM_NAMESPACE" -p '{"spec":{"traffic":{"enableEgress":false}}}' --type=merge
```

#### Enable Egress Policy

```bash
export FSM_NAMESPACE=fsm-system
kubectl patch meshconfig fsm-mesh-config -n "$FSM_NAMESPACE" -p '{"spec":{"featureFlags":{"enableEgressPolicy":true}}}' --type=merge
```

#### Create Egress mTLS Secret

```bash
curl -s https://raw.githubusercontent.com/flomesh-io/fsm-docs/{{< param fsm_branch >}}/manifests/samples/bidir-mtls/certs/pipy-ca.crt -o pipy-ca.crt
curl -s https://raw.githubusercontent.com/flomesh-io/fsm-docs/{{< param fsm_branch >}}/manifests/samples/bidir-mtls/certs/middle.crt -o middle.crt
curl -s https://raw.githubusercontent.com/flomesh-io/fsm-docs/{{< param fsm_branch >}}/manifests/samples/bidir-mtls/certs/middle.key -o middle.key

kubectl create secret generic -n fsm-system egress-middle-cert \
  --from-file=ca.crt=./pipy-ca.crt \
  --from-file=tls.crt=./middle.crt \
  --from-file=tls.key=./middle.key 
```

#### Setup Egress Policy

```bash
kubectl apply -f - <<EOF
kind: Egress
apiVersion: policy.flomesh.io/v1alpha1
metadata:
  name: server-8443
  namespace: egress-middle
spec:
  sources:
  - kind: ServiceAccount
    name: middle
    namespace: egress-middle
    mtls:
      issuer: other
      cert:
        sn: 1
        subjectAltNames: 
        - flomesh.io
        expiration: 2030-1-1 00:00:00
        secret:
          name: egress-middle-cert
          namespace: fsm-system
  hosts:
  - server.egress-server.svc.cluster.local
  ports:
  - number: 8443
    protocol: http
EOF
```

#### Test Commands

Traffic flow:

Client --**http**--> FSM Ingress --**mtls**--> sidecar --> Middle --> sidecar --**egress mtls**--> Server

```bash
kubectl exec "$(kubectl get pod -n egress-client -l app=client -o jsonpath='{.items..metadata.name}')" -n egress-client -- curl -si http://fsm-ingress.fsm-system/time
```

#### Test Results

The correct return result is similar to :

```bash
HTTP/1.1 200 OK
date: Fri, 09 Dec 2022 08:03:59 GMT
content-type: text/plain; charset=utf-8
fsm-stats-namespace: egress-middle
fsm-stats-kind: Deployment
fsm-stats-name: middle
fsm-stats-pod: middle-7956998bd5-bm5vx
content-length: 77
connection: keep-alive

The current time: 2022-12-09 08:03:59.990118972 +0000 UTC m=+21257.813505728
```

This business scenario is tested and the strategy is cleaned up to avoid affecting subsequent tests

```bash
export FSM_NAMESPACE=fsm-system
kubectl patch meshconfig fsm-mesh-config -n "$FSM_NAMESPACE" -p '{"spec":{"certificate":{"ingressGateway":null}}}' --type=merge

kubectl delete ingress -n egress-middle egress-middle
kubectl delete ingressbackend -n egress-middle egress-middle
kubectl delete egress -n egress-middle server-8443
kubectl delete secrets -n fsm-system egress-middle-cert
```


### Scenario#3：TLS FSM Ingress & mTLS Ingress & mTLS Egress

#### Test Commands

Traffic flow:

Client --**http**--> FSM Ingress Controller

```bash
kubectl exec "$(kubectl get pod -n egress-client -l app=client -o jsonpath='{.items..metadata.name}')" -n egress-client -- curl -si http://fsm-ingress.fsm-system/hello
```

#### Test Results

The correct return result is similar to :

```bash
HTTP/1.1 404 Not Found
Server: pipy/0.70.0
content-length: 17
connection: keep-alive

Service Not Found
```

#### Setup Ingress Controller Cert

```bash
export FSM_NAMESPACE=fsm-system
kubectl patch meshconfig fsm-mesh-config -n "$FSM_NAMESPACE" -p '{"spec":{"certificate":{"ingressGateway":{"secret":{"name":"ingress-controller-cert","namespace":"fsm-system"},"subjectAltNames":["fsm.fsm-system.cluster.local"],"validityDuration":"24h"}}}}' --type=merge
```

#### Create Ingress TLS Secret

```bash
curl -s https://raw.githubusercontent.com/flomesh-io/fsm-docs/{{< param fsm_branch >}}/manifests/samples/bidir-mtls/certs/pipy-ca.crt -o pipy-ca.crt
curl -s https://raw.githubusercontent.com/flomesh-io/fsm-docs/{{< param fsm_branch >}}/manifests/samples/bidir-mtls/certs/ingress-pipy.crt -o ingress-pipy.crt
curl -s https://raw.githubusercontent.com/flomesh-io/fsm-docs/{{< param fsm_branch >}}/manifests/samples/bidir-mtls/certs/ingress-pipy.key -o ingress-pipy.key
kubectl create secret generic -n egress-middle ingress-pipy-cert-secret \
  --from-file=ca.crt=./pipy-ca.crt \
  --from-file=tls.crt=./ingress-pipy.crt \
  --from-file=tls.key=./ingress-pipy.key
```

#### Create Egress mTLS Secret

```bash
curl -s https://raw.githubusercontent.com/flomesh-io/fsm-docs/{{< param fsm_branch >}}/manifests/samples/bidir-mtls/certs/pipy-ca.crt -o pipy-ca.crt
curl -s https://raw.githubusercontent.com/flomesh-io/fsm-docs/{{< param fsm_branch >}}/manifests/samples/bidir-mtls/certs/middle.crt -o middle.crt
curl -s https://raw.githubusercontent.com/flomesh-io/fsm-docs/{{< param fsm_branch >}}/manifests/samples/bidir-mtls/certs/middle.key -o middle.key

kubectl create secret generic -n fsm-system egress-middle-cert \
  --from-file=ca.crt=./pipy-ca.crt \
  --from-file=tls.crt=./middle.crt \
  --from-file=tls.key=./middle.key
```

#### Setup Ingress Rules

```bash
kubectl apply -f - <<EOF
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: egress-middle
  namespace: egress-middle
  annotations:
    # upstream-ssl-name for a service is of the form <service-account>.<namespace>.cluster.local
    pipy.ingress.kubernetes.io/upstream-ssl-name: "middle.egress-middle.cluster.local"
    pipy.ingress.kubernetes.io/upstream-ssl-secret: "fsm-system/ingress-controller-cert"
    pipy.ingress.kubernetes.io/upstream-ssl-verify: "on"
spec:
  ingressClassName: pipy
  rules:
  - http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: middle
            port:
              number: 8080
  tls:
  - hosts:
    - fsm-ingress.fsm-system
    secretName: ingress-pipy-cert-secret
EOF
```

#### Setup IngressBackend Policy

```bash
kubectl apply -f - <<EOF
kind: IngressBackend
apiVersion: policy.flomesh.io/v1alpha1
metadata:
  name: egress-middle
  namespace: egress-middle
spec:
  backends:
  - name: middle
    port:
      number: 8080 # targetPort of middle service
      protocol: https
    tls:
      skipClientCertValidation: false
  sources:
  - kind: Service
    namespace: fsm-system
    name: fsm-ingress
  - kind: AuthenticatedPrincipal
    name: fsm.fsm-system.cluster.local
EOF
```

#### Replace client TLS

```shell
curl -s https://raw.githubusercontent.com/flomesh-io/fsm-docs/{{< param fsm_branch >}}/manifests/samples/bidir-mtls/certs/client.crt -o client.crt
curl -s https://raw.githubusercontent.com/flomesh-io/fsm-docs/{{< param fsm_branch >}}/manifests/samples/bidir-mtls/certs/client.key -o client.key

kubectl create secret generic -n egress-client egress-client-secret \
  --from-file=ca.crt=./pipy-ca.crt \
  --from-file=tls.crt=./client.crt \
  --from-file=tls.key=./client.key 

kubectl -n egress-client patch deploy client -p \
  '
  {
    "spec": {
      "template": {
        "spec": {
          "containers": [{
            "name": "client",
            "volumeMounts": [{
              "mountPath": "/client",
              "name": "client-certs"
            }]
          }],
          "volumes": [{
            "secret": {
              "secretName": "egress-client-secret"
            },
            "name": "client-certs"
          }]
        }
      }
    }
  }
  '
```

#### FSM disable inbound mTLS

```shell
kubectl patch meshconfig fsm-mesh-config -n "$FSM_NAMESPACE" -p '{"spec":{"ingress":{"tls":{"mTLS": false}}}}' --type=merge
```

#### Test Commands

Traffic flow:

Client --**tls**--> Ingress FSM --**mtls** --> sidecar --> Middle

```bash
kubectl exec "$(kubectl get pod -n egress-client -l app=client -o jsonpath='{.items..metadata.name}')" -n egress-client -- curl -ksi https://fsm-ingress.fsm-system/hello --key /client/tls.key --cert /client/tls.crt
```

#### Test Results

The correct return result is similar to :

```bash
HTTP/2 200
date: Thu, 15 Dec 2022 07:02:42 GMT
content-type: text/plain; charset=utf-8
fsm-stats-namespace: egress-middle
fsm-stats-kind: Deployment
fsm-stats-name: middle
fsm-stats-pod: middle-5bf7d76c4c-xr24j
content-length: 13

hello world.
```

#### Disable Egress Permissive mode

```bash
export FSM_NAMESPACE=fsm-system
kubectl patch meshconfig fsm-mesh-config -n "$FSM_NAMESPACE" -p '{"spec":{"traffic":{"enableEgress":false}}}' --type=merge
```

#### Enable Egress Policy

```bash
export FSM_NAMESPACE=fsm-system
kubectl patch meshconfig fsm-mesh-config -n "$FSM_NAMESPACE" -p '{"spec":{"featureFlags":{"enableEgressPolicy":true}}}' --type=merge
```

#### Setup Egress Policy

```bash
kubectl apply -f - <<EOF
kind: Egress
apiVersion: policy.flomesh.io/v1alpha1
metadata:
  name: server-8443
  namespace: egress-middle
spec:
  sources:
  - kind: ServiceAccount
    name: middle
    namespace: egress-middle
    mtls:
      issuer: other
      cert:
        sn: 1
        expiration: 2030-1-1 00:00:00
        subjectAltNames: 
        - flomesh.io
        secret:
          name: egress-middle-cert
          namespace: fsm-system
  hosts:
  - server.egress-server.svc.cluster.local
  ports:
  - number: 8443
    protocol: http
EOF
```

#### Test Commands

Traffic flow:

Client --**tls**-->  Ingress FSM --**mtls**--> sidecar --> Middle --> sidecar --**egress mtls**--> Server

```bash
kubectl exec "$(kubectl get pod -n egress-client -l app=client -o jsonpath='{.items..metadata.name}')" -n egress-client -- curl -ksi https://fsm-ingress.fsm-system/time --key /client/tls.key --cert /client/tls.crt
```

#### Test Results

The correct return result is similar to :

```bash
HTTP/2 200
date: Thu, 15 Dec 2022 07:04:26 GMT
content-type: text/plain; charset=utf-8
fsm-stats-namespace: egress-middle
fsm-stats-kind: Deployment
fsm-stats-name: middle
fsm-stats-pod: middle-5bf7d76c4c-xr24j
content-length: 75

The current time: 2022-12-15 07:04:26.62032737 +0000 UTC m=+4972.430170668
```

This business scenario is tested and the strategy is cleaned up to avoid affecting subsequent tests

```bash
export FSM_NAMESPACE=fsm-system
kubectl patch meshconfig fsm-mesh-config -n "$FSM_NAMESPACE" -p '{"spec":{"certificate":{"ingressGateway":null}}}' --type=merge

kubectl delete ingress -n egress-middle egress-middle
kubectl delete ingressbackend -n egress-middle egress-middle
kubectl delete egress -n egress-middle server-8443
kubectl delete secrets -n fsm-system egress-middle-cert
kubectl delete secrets -n egress-middle ingress-pipy-cert-secret
```

### Scenario#4：mTLS FSM & mTLS Ingress & mTLS Egress

#### Test Commands

Traffic flow:

Client --**http**--> FSM Ingress Controller

```bash
kubectl exec "$(kubectl get pod -n egress-client -l app=client -o jsonpath='{.items..metadata.name}')" -n egress-client -- curl -si http://fsm-ingress.fsm-system/hello
```

#### Test Results

The correct return result is similar to :

```bash
HTTP/1.1 404 Not Found
Server: pipy/0.70.0
content-length: 17
connection: keep-alive

Service Not Found
```

#### Setup Ingress Controller Cert

```bash
export FSM_NAMESPACE=fsm-system
kubectl patch meshconfig fsm-mesh-config -n "$FSM_NAMESPACE" -p '{"spec":{"certificate":{"ingressGateway":{"secret":{"name":"ingress-controller-cert","namespace":"fsm-system"},"subjectAltNames":["fsm.fsm-system.cluster.local"],"validityDuration":"24h"}}}}' --type=merge
```

#### Create FSM TLS Secret and CA Secret

```bash
curl -s https://raw.githubusercontent.com/flomesh-io/fsm-docs/{{< param fsm_branch >}}/manifests/samples/bidir-mtls/certs/pipy-ca.crt -o pipy-ca.crt
curl -s https://raw.githubusercontent.com/flomesh-io/fsm-docs/{{< param fsm_branch >}}/manifests/samples/bidir-mtls/certs/ingress-pipy.crt -o ingress-pipy.crt
curl -s https://raw.githubusercontent.com/flomesh-io/fsm-docs/{{< param fsm_branch >}}/manifests/samples/bidir-mtls/certs/ingress-pipy.key -o ingress-pipy.key

kubectl create secret generic -n egress-middle ingress-pipy-cert-secret \
  --from-file=ca.crt=./pipy-ca.crt \
  --from-file=tls.crt=./ingress-pipy.crt \
  --from-file=tls.key=./ingress-pipy.key

kubectl create secret generic -n egress-middle ingress-controller-ca-secret \
  --from-file=ca.crt=./pipy-ca.crt
```

#### Replace client TLS certificate

```shell

curl -s https://raw.githubusercontent.com/flomesh-io/fsm-docs/{{< param fsm_branch >}}/manifests/samples/bidir-mtls/certs/client.crt -o client.crt
curl -s https://raw.githubusercontent.com/flomesh-io/fsm-docs/{{< param fsm_branch >}}/manifests/samples/bidir-mtls/certs/client.key -o client.key

kubectl create secret generic -n egress-client egress-client-secret \
  --from-file=ca.crt=./pipy-ca.crt \
  --from-file=tls.crt=./client.crt \
  --from-file=tls.key=./client.key 

kubectl -n egress-client patch deploy client -p \
  '
  {
    "spec": {
      "template": {
        "spec": {
          "containers": [{
            "name": "client",
            "volumeMounts": [{
              "mountPath": "/client",
              "name": "client-certs"
            }]
          }],
          "volumes": [{
            "secret": {
              "secretName": "egress-client-secret"
            },
            "name": "client-certs"
          }]
        }
      }
    }
  }
  '
```

#### Setup Ingress Rules

```bash
kubectl apply -f - <<EOF
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: egress-middle
  namespace: egress-middle
  annotations:
    # mTLS
    pipy.ingress.kubernetes.io/tls-trusted-ca-secret: egress-middle/ingress-controller-ca-secret
    pipy.ingress.kubernetes.io/tls-verify-client: "on"
    pipy.ingress.kubernetes.io/tls-verify-depth: "1"

    # upstream-ssl-name for a service is of the form <service-account>.<namespace>.cluster.local
    pipy.ingress.kubernetes.io/upstream-ssl-name: "middle.egress-middle.cluster.local"
    pipy.ingress.kubernetes.io/upstream-ssl-secret: "fsm-system/ingress-controller-cert"
    pipy.ingress.kubernetes.io/upstream-ssl-verify: "on"
spec:
  ingressClassName: pipy
  rules:
  - http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: middle
            port:
              number: 8080
  tls:
  - hosts:
    - fsm-ingress.fsm-system
    secretName: ingress-pipy-cert-secret
EOF
```

#### Setup IngressBackend Policy

```bash
kubectl apply -f - <<EOF
kind: IngressBackend
apiVersion: policy.flomesh.io/v1alpha1
metadata:
  name: egress-middle
  namespace: egress-middle
spec:
  backends:
  - name: middle
    port:
      number: 8080 # targetPort of middle service
      protocol: https
    tls:
      skipClientCertValidation: false
  sources:
  - kind: Service
    namespace: fsm-system
    name: fsm-ingress
  - kind: AuthenticatedPrincipal
    name: fsm.fsm-system.cluster.local
EOF
```

#### FSM enable inbound mTLS

```shell
kubectl patch meshconfig fsm-mesh-config -n "$FSM_NAMESPACE" -p '{"spec":{"ingress":{"tls":{"mTLS": true}}}}' --type=merge
```

#### Test Commands

Traffic flow:

Client --**mtls**--> Ingress FSM --**mtls** --> sidecar --> Middle

```bash
kubectl exec "$(kubectl get pod -n egress-client -l app=client -o jsonpath='{.items..metadata.name}')" -n egress-client -- curl -ksi https://fsm-ingress.fsm-system/hello  --cacert /client/ca.crt --key /client/tls.key --cert /client/tls.crt
```

#### Test Results

The correct return result is similar to :

```bash
HTTP/2 200
date: Thu, 15 Dec 2022 08:55:01 GMT
content-type: text/plain; charset=utf-8
fsm-stats-namespace: egress-middle
fsm-stats-kind: Deployment
fsm-stats-name: middle
fsm-stats-pod: middle-5bf7d76c4c-xr24j
content-length: 13

hello world.
```

#### Disable Egress Permissive mode

```bash
export FSM_NAMESPACE=fsm-system
kubectl patch meshconfig fsm-mesh-config -n "$FSM_NAMESPACE" -p '{"spec":{"traffic":{"enableEgress":false}}}' --type=merge
```

#### Enable Egress Policy

```bash
export FSM_NAMESPACE=fsm-system
kubectl patch meshconfig fsm-mesh-config -n "$FSM_NAMESPACE" -p '{"spec":{"featureFlags":{"enableEgressPolicy":true}}}' --type=merge
```

#### Create Egress mTLS Secret

```bash
curl https://raw.githubusercontent.com/flomesh-io/fsm-docs/{{< param fsm_branch >}}/manifests/samples/bidir-mtls/certs/pipy-ca.crt -o pipy-ca.crt
curl https://raw.githubusercontent.com/flomesh-io/fsm-docs/{{< param fsm_branch >}}/manifests/samples/bidir-mtls/certs/middle.crt -o middle.crt
curl https://raw.githubusercontent.com/flomesh-io/fsm-docs/{{< param fsm_branch >}}/manifests/samples/bidir-mtls/certs/middle.key -o middle.key

kubectl create secret generic -n fsm-system egress-middle-cert \
  --from-file=ca.crt=./pipy-ca.crt \
  --from-file=tls.crt=./middle.crt \
  --from-file=tls.key=./middle.key
```

#### Setup Egress Policy

```bash
kubectl apply -f - <<EOF
kind: Egress
apiVersion: policy.flomesh.io/v1alpha1
metadata:
  name: server-8443
  namespace: egress-middle
spec:
  sources:
  - kind: ServiceAccount
    name: middle
    namespace: egress-middle
    mtls:
      issuer: other
      cert:
        sn: 1
        expiration: 2030-1-1 00:00:00
        secret:
          name: egress-middle-cert
          namespace: fsm-system
  hosts:
  - server.egress-server.svc.cluster.local
  ports:
  - number: 8443
    protocol: http
EOF
```

#### Test Commands

Traffic flow:

Client --**mtls**--> Ingress FSM --**mtls**--> sidecar --> Middle --> sidecar --**egress mtls**--> Server

```bash
kubectl exec "$(kubectl get pod -n egress-client -l app=client -o jsonpath='{.items..metadata.name}')" -n egress-client -- curl -ksi https://fsm-ingress.fsm-system/time --cacert /client/ca.crt --key /client/tls.key --cert /client/tls.crt
```

#### Test Results

The correct return result is similar to :

```bash
HTTP/2 200
date: Thu, 15 Dec 2022 08:56:12 GMT
content-type: text/plain; charset=utf-8
fsm-stats-namespace: egress-middle
fsm-stats-kind: Deployment
fsm-stats-name: middle
fsm-stats-pod: middle-5bf7d76c4c-xr24j
content-length: 76

The current time: 2022-12-15 08:56:12.953677725 +0000 UTC m=+6997.289902113
```