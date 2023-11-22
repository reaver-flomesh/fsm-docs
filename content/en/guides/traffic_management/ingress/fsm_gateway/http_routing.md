---
title: "HTTP Routing"
description: "Route HTTP Traffic."
type: docs
weight: 2
draft: false
---


In FSM Gateway, the [HTTPRoute resource](https://gateway-api.sigs.k8s.io/api-types/httproute) is used to configure route rules which will match request to backend servers. Currently, the Kubernetes Service is the only one accepted as backend resource.

## Prerequisites

- Kubernetes cluster version {{< param min_k8s_version_gateway_api >}} or higher.
- kubectl CLI
- FSM Gateway installed via [guide doc](/guides/traffic_management/ingress/fsm_gateway/installation).

## Demonstration

### Deploy sample

First, let's install the example in namespace `httpbin` with commands below.

```bash
kubectl create namespace httpbin
kubectl apply -n httpbin -f https://raw.githubusercontent.com/flomesh-io/fsm-docs/{{< param fsm_branch >}}/manifests/gateway/http-routing.yaml
```

### Verification 

Once done, we can get the gateway installed.

```bash
kubectl get pod,svc -n httpbin -l app=fsm-gateway                                                                                           default ⎈
NAME                                       READY   STATUS    RESTARTS   AGE
pod/fsm-gateway-httpbin-867768f76c-69s6x   1/1     Running   0          16m

NAME                          TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)          AGE
service/fsm-gateway-httpbin   LoadBalancer   10.43.41.36   10.0.2.4      8000:31878/TCP   16m
```

Beyond the gateway resources, we also create the HTTPRoute resources.

```bash
kubectl get httproute -n httpbin
NAME             HOSTNAMES             AGE
http-route-foo   ["foo.example.com"]   18m
http-route-bar   ["bar.example.com"]   18m
```

### Testing

To test the rules, we should get the address of gateway first.

```bash
export GATEWAY_IP=$(kubectl get svc -n httpbin -l app=fsm-gateway -o jsonpath='{.items[0].status.loadBalancer.ingress[0].ip}')
```

We can trigger a request to gateway without hostname.

```bash
curl -i http://$GATEWAY_IP:8000/headers
HTTP/1.1 404 Not Found
server: pipy-repo
content-length: 0
connection: keep-alive
```

It responds with `404`. Next, we can try with the hostnames configured in HTTPRoute resources.

```bash
curl -H 'host:foo.example.com' http://$GATEWAY_IP:8000/headers
{
  "headers": {
    "Accept": "*/*",
    "Connection": "keep-alive",
    "Host": "foo.example.com",
    "User-Agent": "curl/7.68.0"
  }
}

curl -H 'host:bar.example.com' http://$GATEWAY_IP:8000/headers
{
  "headers": {
    "Accept": "*/*",
    "Connection": "keep-alive",
    "Host": "bar.example.com",
    "User-Agent": "curl/7.68.0"
  }
}
```

This time, the server responds success message. There is hostname we are requesting in each response.