---
title: "TLS Termination"
description: ""
type: docs
weight: 14
draft: false
---

TLS offloading is the process of terminating TLS connections at a load balancer or gateway, decrypting the traffic and passing it to the backend server, thereby relieving the backend server of the encryption and decryption burden.

This doc will show you how to use TSL termination for service.

## Prerequisites

- Kubernetes cluster version {{< param min_k8s_version_gateway_api >}} or higher.
- kubectl CLI
- FSM Gateway installed via [guide doc](/guides/traffic_management/ingress/fsm_gateway/installation).

## Demonstration

```bash
export GATEWAY_IP=$(kubectl get svc -n httpbin -l app=fsm-gateway -o jsonpath='{.items[0].status.loadBalancer.ingress[0].ip}')
```

### Issue TLS certificate

If configure TLS, a certificate is required. Let's issue a certificate first.

```shell
openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 \
  -keyout example.com.key -out example.com.crt \
  -subj "/CN=example.com"
```

With command above executed, you will get two files `example.com.crt` and `example.com.key` which we can create a secret with.

```shell
kubectl create namespace httpbin
kubectl create secret tls simple-gateway-cert --key=example.com.key --cert=example.com.crt -n httpbin
```

### Deploy sample app

```shell
kubectl apply -n httpbin -f https://raw.githubusercontent.com/flomesh-io/fsm-docs/{{< param fsm_branch >}}/manifests/gateway/tls-termination.yaml
```

### Test

```shell
curl --cacert example.com.crt https://example.com/headers  --connect-to example.com:443:$GATEWAY_IP:8000
{
  "headers": {
    "Accept": "*/*",
    "Connection": "keep-alive",
    "Host": "example.com",
    "User-Agent": "curl/7.68.0"
  }
}

```