---
title: "Upstream TLS"
description: "A network architecture using HTTP for client communication and HTTPS upstream, featuring SSL/TLS termination at the gateway and centralized certificate management for enhanced security."
type: docs
weight: 36
draft: false
---

Using HTTP for external client communication and HTTPS for upstream services is a common network architecture pattern. In this setup, the gateway acts as the SSL/TLS termination point, ensuring secure encrypted communication with upstream services. This means that even though the client-to-gateway communication uses standard unencrypted HTTP protocol, the gateway securely converts these requests to HTTPS for communication with upstream services.

Centralized certificate management simplifies security maintenance, enhancing system reliability and manageability. This pattern is particularly practical in scenarios requiring protected internal communication while balancing front-end compatibility and performance.

## Prerequisites

- Kubernetes cluster
- kubectl tool
- FSM Gateway installed via [guide doc](/guides/traffic_management/ingress/fsm_gateway/installation).

## Demonstration

### Deploying Sample Application

Our upstream application uses HTTPS, so first, generate a self-signed certificate. The following commands generate a CA certificate, server certificate, and key.

```shell
openssl genrsa 2048 > ca-key.pem

openssl req -new -x509 -nodes -days 365000 \
   -key ca-key.pem \
   -out ca-cert.pem \
   -subj '/CN=flomesh.io'

openssl genrsa -out server-key.pem 2048
openssl req -new -key server-key.pem -out server.csr -subj '/CN=foo.example.com'
openssl x509 -req -in server.csr -CA ca-cert.pem -CAkey ca-key.pem -CAcreateserial -out server-cert.pem -days 365
```

Create a Secret `server-cert` using the server certificate and key.

```shell
kubectl create namespace httpbin
#TLS cert secret
kubectl create secret generic -n httpbin server-cert \
  --from-file=./server-cert.pem \
  --from-file=./server-key.pem

```

The sample application still uses the httpbin image, but now with TLS enabled using the created certificate and key.

```shell
kubectl apply -n httpbin -f - <<EOF
apiVersion: apps/v1
kind: Deployment
metadata:
  name: httpbin
spec:
  replicas: 1
  selector:
    matchLabels:
      app: httpbin
  template:
    metadata:
      labels:
        app: httpbin
    spec:
      containers:
      - name: httpbin
        image: kennethreitz/httpbin
        ports:
        - containerPort: 443
        volumeMounts:
        - name: cert-volume
          mountPath: /etc/httpbin/certs  # Mounting path in the container
        command: ["gunicorn"]
        args: ["-b", "0.0.0.0:443", "httpbin:app", "-k", "gevent", "--certfile", "/etc/httpbin/certs/server-cert.pem", "--keyfile", "/etc/httpbin/certs/server-key.pem"]
      volumes:
      - name: cert-volume
        secret:
          secretName: server-cert
---
apiVersion: v1
kind: Service
metadata:
  name: httpbin
spec:
  selector:
    app: httpbin
  ports:
    - protocol: TCP
      port: 8443
      targetPort: 443
EOF
```

Verify if HTTPS has been enabled.

```shell
export HTTPBIN_POD=$(kubectl get po -n httpbin -l app=httpbin -o jsonpath='{.items[0].metadata.name}')

kubectl port-forward -n httpbin $HTTPBIN_POD 8443:443 &
# access with CA cert
curl --cacert ca-cert.pem https://foo.example.com/headers --connect-to foo.example.com:443:127.0.0.1:8443
{
  "headers": {
    "Accept": "*/*",
    "Host": "foo.example.com",
    "User-Agent": "curl/8.1.2"
  }
}
```

### Creating Gateway and Routes

Next, create a gateway and route for the Service `httpbin`.

```shell
kubectl apply -n httpbin -f - <<EOF
apiVersion: gateway.networking.k8s.io/v1
kind: Gateway
metadata:
  name: simple-fsm-gateway
spec:
  gatewayClassName: fsm-gateway-cls
  listeners:
  - protocol: HTTP
    port: 8000
    name: http
    allowedRoutes:
      namespaces:
        from: Same
---
apiVersion: gateway.networking.k8s.io/v1
kind: HTTPRoute
metadata:
  name: http-route-foo
spec:
  parentRefs:
  - name: simple-fsm-gateway
    port: 8000
  hostnames:
  - foo.example.com
  rules:
  - matches:
    - path:
        type: PathPrefix
        value: /
    backendRefs:
    - name: httpbin
      port: 8443
EOF
```

At this point, accessing `httpbin` through the gateway is not possible, as `httpbin` has TLS enabled and the gateway cannot verify its server certificate.

```shell
curl http://foo.example.com/headers --connect-to foo.example.com:80:$GATEWAY_IP:8000
curl: (52) Empty reply from server
```

## Upstream TLS Policy Verification

Create a Secret `https-cert` using the previously created CA certificate.

```shell
#CA cert secret
kubectl create secret generic -n httpbin https-cert --from-file=ca.crt=ca-cert.pem
```

Next, create an Upstream TLS Policy `UpstreamTLSPolicy`. Refer to the document [UpstreamTLSPolicy](/api_reference/policyattachment/v1alpha1/#gateway.flomesh.io/v1alpha1.UpstreamTLSPolicy) and specify the Secret `https-cert`, containing the CA certificate, for the upstream service `httpbin`. The gateway will use this certificate to verify `httpbin`'s server certificate.

```shell
kubectl apply -n httpbin -f - <<EOF
apiVersion: gateway.flomesh.io/v1alpha1
kind: UpstreamTLSPolicy
metadata:
  name: upstream-tls-policy-sample
spec:
  targetRef:
    group: ""
    kind: Service
    name: httpbin
    namespace: httpbin
  ports:
  - port: 8443
    config:
      certificateRef:
        namespace: httpbin
        name: https-cert
      mTLS: false
EOF
```

After applying this policy and once it takes effect, try accessing the `httpbin` service through the gateway again.

```shell
curl http://foo.example.com/headers --connect-to foo.example.com:80:$GATEWAY_IP:8000
{
  "headers": {
    "Accept": "*/*",
    "Connection": "keep-alive",
    "Host": "10.42.0.25:443",
    "User-Agent": "curl/8.1.2"
  }
}
```
