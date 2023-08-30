---
title: "Ingress Controller - Basics"
description: "Using FSM Ingress controller to serve HTTP/HTTPS traffic"
type: docs
weight: 10
---

This guide demonstrate how to serve HTTP and HTTPs traffic via FSM Ingress controller.

## Prerequisites

- Kubernetes cluster version {{< param min_k8s_version >}} or higher.
- Interact with the API server using `kubectl`.
- FSM CLI installed.
- FSM Ingress Controller installed followed by [installation document](/guides/traffic_management/ingress/kubernetes_ingress/#installation)

### Sample Application

The example application used here provides access through both **HTTP** at port `8000` and **HTTPS** at port `8443`, with the following URI:

- `/` returns a simple HTML page
- `/hi` returns a `200` response with string `Hi, there!`
- `/api/private` returns a `401` response with string `Staff only`


To provide HTTPS, a CA certificate and server certificate need to be issued for the application first.

```shell
openssl genrsa 2048 > ca-key.pem

openssl req -new -x509 -nodes -days 365000 \
   -key ca-key.pem \
   -out ca-cert.pem \
   -subj '/CN=flomesh.io'

openssl genrsa -out server-key.pem 2048
openssl req -new -key server-key.pem -out server.csr -subj '/CN=example.com'
openssl x509 -req -in server.csr -CA ca-cert.pem -CAkey ca-key.pem -CAcreateserial -out server-cert.pem -days 365
```

Before deploying the sample service, first let's create a `secret` to save the certificate and key in the secret and mount it in the application pod.

```yaml
kubectl create namespace httpbin
# mount self-signed cert to sample app pod via secret
kubectl create secret generic -n httpbin server-cert \
  --from-file=./server-cert.pem \
  --from-file=./server-key.pem

kubectl apply -n httpbin -f - <<EOF
apiVersion: v1
kind: ServiceAccount
metadata:
  name: httpbin
---
apiVersion: v1
kind: Service
metadata:
  name: httpbin
  labels:
    app: httpbin
    service: httpbin
spec:
  ports:
  - port: 8443
    name: https
  - port: 8000
    name: http
  selector:
    app: httpbin
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: httpbin
  labels:
    app: httpbin
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
        - name: pipy
          image: addozhang/httpbin:latest
          env:
            - name: PIPY_CONFIG_FILE
              value: /etc/pipy/tutorial/gateway/main.js
          ports:
            - containerPort: 8443
            - containerPort: 8000
          volumeMounts:
          - name: cert
            mountPath: "/etc/pipy/tutorial/gateway/secret"
            readOnly: true
      volumes:
      - name: cert
        secret:
          secretName: server-cert
EOF
```

## Basic configurations

### HTTP Protocol

In the following example, an `Ingress` resource is defined that routes requests with **host** `example.com` and **path** `/get` and `/ `to the back-end service `httpbin` listening at port `8000`.

>Note that the `Ingress` resource and the back-end service should belong to the same namespace.

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: httpbin
spec:
  ingressClassName: pipy
  rules:
  - host: example.com
    http:
      paths:
        - path: /
          pathType: Exact
          backend:
            service:
              name: httpbin
              port:
                number: 8000
        - path: /hi
          pathType: Exact
          backend:
            service:
              name: httpbin
              port:
                number: 8000
```

Explanation of some of fields:

- `metadata.name` field defines the resource name of the Ingress.
- `spec.ingressClassName` field is used to specify the implementation of the entrance controller. `Ingressclass` is the name defined by the implementation of each entrance controller, and here we use `pipy`. The installed entrance controllers can be viewed through `kubectl get ingressclass`.
- `spec.rules` field is used to define the routing resource.
  - `host` field defines the hostname `example.com`
  - `paths` field defines two path rules: the request matching the path `/`, and the uri `/hi`
  - `backend` field defines the backend service `httpbin` and port `8000` used to handle the path rule.

By viewing the Ingress Controller Service, you can see that its type is `LoadBalancer`, and its external address is `10.0.0.12`, which is exactly the node's IP address.

```shell
kubectl get svc -n fsm-system -l app=fsm-ingress
NAME          TYPE           CLUSTER-IP      EXTERNAL-IP   PORT(S)        AGE
fsm-ingress   LoadBalancer   10.43.243.124   10.0.2.4      80:30508/TCP   16h
```

Applying the Ingress configuration above, when accessing the uri `/hi` and `/` endpoints of the `httpbin` service, we can use the node's IP address and port `80`.

```shell
export HOST_IP=10.0.0.12
curl http://example.com/hi --connect-to example.com:80:$HOST_IP:80
Hi, there!

curl http://example.com/ --connect-to example.com:80:$HOST_IP:80
<!DOCTYPE html>
<html>
  <head>
    <title>Hi, Pipy!</title>
  </head>
  <body>
    <h1>Hi, Pipy!</h1>
    <p>This is a web page served from Pipy.</p>
  </body>
</html>
```

### HTTPS protocol

This example shows how to configure an ingress controller to support **HTTPS** access. By default, the **FSM Ingress** does not enable TLS ingress, and you need to turn on the TLS ingress functionality by using the parameter `--set fsm.ingress.tls.enabled=true` during installation.

Or execute the command below to enable ingress TLS after installed.

```bash
export FSM_NAMESPACE=fsm-system
kubectl patch meshconfig fsm-mesh-config -n "$FSM_NAMESPACE" -p '{"spec":{"ingress":{"tls":{"enabled":true}}}}' --type=merge
```

The Ingress resource in the following example configures the url `https://example.com` to access ingress.

- `spec.tls` is the exclusive field for TLS configuration and can configure multiple HTTPS ingresses. 
- `hosts` field is used to configure SNI and can configure multiple SNIs. Here, `example.com` is used, and wildcard `*.example.com` is also supported. 
- `secretName` field is used to specify the `Secret` that stores the certificate and key. Note that the Ingress resource and Secret should belong to the same namespace.

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: httpbin
spec:
  ingressClassName: pipy
  rules:
  - host: example.com
    http:
      paths:
        - path: /
          pathType: Prefix
          backend:
            service:
              name: httpbin
              port:
                number: 8000
  tls:
  - hosts:
    - example.com
    secretName: ingress-cert
```

Issue TLS certificate

```shell
openssl req -x509 -newkey rsa:4096 -keyout ingress-key.pem -out ingress-cert.pem -sha256 -days 365 -nodes -subj '/CN=example.com'
```

Create a `Secret` using the certificate and key.

```shell
kubectl create secret generic -n httpbin ingress-cert \
  --from-file=tls.crt=./ingress-cert.pem --from-file=tls.key=ingress-key.pem
```

Apply above configuration changes. Test service using the `ingress-cert.pem` as the CA certificate to make the request, noting that the Ingress mTLS feature is currently disabled.

```shell
curl --cacert ingress-cert.pem https://example.com/hi --connect-to example.com:443:$HOST_IP:443
Hi, there!
```

## Advanced Configuration

Next, we will introduce the advanced configuration of FSM Ingress. Advanced configuration is set through the `metadata.annotations` field of the `Ingress` resource. The currently supported features are:

- Path Rewrite
- Specifying Load Balancing Algorithm
- Session Persistence

### Path Rewrite

This example demonstrates how to use the rewrite path annotation.

FSM provides two annotations, `pipy.ingress.kubernetes.io/rewrite-target-from` and `pipy.ingress.kubernetes.io/rewrite-target-to`, to configure path rewrite, both of these are required, when used.

In the following example, a route rule defines that requests with a path prefix of `/httpbin` will be routed to the `14001` port of the `httpbin` service, but the service itself does not have this path. This is where the path rewrite feature comes in.

- `pipy.ingress.kubernetes.io/rewrite-target-from: ^/httpbin/?` ，This supports regular expressions, where the starting path of `/httpbin/` is rewritten.
- `pipy.ingress.kubernetes.io/rewrite-target-to: /`，Here, the specified rewrite content is `/`.

In summary, the path starting with `/httpbin/` will be replaced with `/`, for example, `/httpbin/get` will be rewritten as `/get`.

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: httpbin
  annotations:
    pipy.ingress.kubernetes.io/rewrite-target-from: ^/httpbin/?
    pipy.ingress.kubernetes.io/rewrite-target-to: /
spec:
  ingressClassName: pipy
  rules:
  - host: example.com
    http:
      paths:
        - path: /httpbin
          pathType: Prefix
          backend:
            service:
              name: httpbin
              port:
                number: 8000
```

After applying above Ingress configuration, we now can use the path `/httpbin/hi` to access the `/hi` endpoint of the `httpbin` service.

```shell
curl http://example.com/httpbin/hi --connect-to example.com:80:$HOST_IP:80
Hi, there!
```

### Specifying Load Balancing Algorithm

This example demonstrates specifying the load balancing algorithm when you have multiple replicas running and you want to distribute load among them.

By default, FSM Ingress uses the **Round-Robin** load balancing algorithm, but other algorithms can be specified using the annotation `pipy.ingress.kubernetes.io/lb-type annotation`. Other supported load balancing algorithms are:

- `round-robin`
- `hashing`
- `least-work`

In the following example, the `hashing` load balancing algorithm is specified through the annotation.

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: httpbin
  annotations:
    pipy.ingress.kubernetes.io/lb-type: 'hashing'
spec:
  ingressClassName: pipy
  rules:
  - host: example.com
    http:
      paths:
        - path: /
          pathType: Prefix
          backend:
            service:
              name: httpbin
              port:
                number: 8000
```

To demonstrate the effect of the demonstration, deploy the following example application, which has two instances and carries the hostname in the response to distinguish the response request example.

```shell
kubectl create namespace httpbin
kubectl apply -n httpbin -f - <<EOF
apiVersion: v1
kind: ServiceAccount
metadata:
  name: httpbin
---
apiVersion: v1
kind: Service
metadata:
  name: httpbin
  labels:
    app: httpbin
    service: httpbin
spec:
  ports:
  - name: http
    port: 8000
  selector:
    app: httpbin
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: httpbin
  labels:
    app: httpbin
spec:
  replicas: 2
  selector:
    matchLabels:
      app: httpbin
  template:
    metadata:
      labels:
        app: httpbin
    spec:
      containers:
      - name: pipy
        image: addozhang/httpbin:latest
        ports:
          - containerPort: 8000
        command:
        - pipy
        - -e
        - |
          pipy()
          .listen(8000)
          .serveHTTP(new Message('Response from pod ' + os.env["HOSTNAME"]))
EOF
```

Apply above configuration and send requests to test the configuration.

```shell
curl http://example.com/ --connect-to example.com:80:$HOST_IP:80
Response from pod httpbin-5f69c44674-t9cxc
curl http://example.com/ --connect-to example.com:80:$HOST_IP:80
Response from pod httpbin-5f69c44674-t9cxc
curl http://example.com/ --connect-to example.com:80:$HOST_IP:80
Response from pod httpbin-5f69c44674-t9cxc
```

### Session Persistence

This example demonstrates session persistence functionality.

FSM Ingress provides the annotation `pipy.ingress.kubernetes.io/session-sticky` to configure session persistence, with a default value of `false` (equivalent to `no`, `0`, `off`, or ` `), meaning that the session is not kept. If you need to keep the session, you need to set the value to `true`, `yes`, `1`, or `on`.

For example, in the following example, the annotation value is set to `true`, used to maintain the session between the two instances of the backend service.

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: httpbin
  annotations:
    pipy.ingress.kubernetes.io/session-sticky: 'true'
spec:
  ingressClassName: pipy
  rules:
  - host: example.com
    http:
      paths:
        - path: /
          pathType: Prefix
          backend:
            service:
              name: httpbin
              port:
                number: 8000
```

In order to demonstrate the effect, deploy the following example application, which has two instances and carries the host name in the response to differentiate the response request.

```shell
kubectl create namespace httpbin
kubectl apply -n httpbin -f - <<EOF
apiVersion: v1
kind: ServiceAccount
metadata:
  name: httpbin
---
apiVersion: v1
kind: Service
metadata:
  name: httpbin
  labels:
    app: httpbin
    service: httpbin
spec:
  ports:
  - name: http
    port: 8000
  selector:
    app: httpbin
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: httpbin
  labels:
    app: httpbin
spec:
  replicas: 2
  selector:
    matchLabels:
      app: httpbin
  template:
    metadata:
      labels:
        app: httpbin
    spec:
      containers:
        - name: pipy
          image: addozhang/httpbin:latest
          ports:
            - containerPort: 8000
          command:
            - pipy
            - -e
            - |
              pipy()
              .listen(8000)
              .serveHTTP(new Message('Response from pod ' + os.env["HOSTNAME"]))
EOF
```

Applying the above Ingress configuration, send multiple requests to test and it can be observed that it's always the same instance responding to the request.

```shell
curl http://example.com/ --connect-to example.com:80:$HOST_IP:80
Response from pod httpbin-5f69c44674-hrvqp
curl http://example.com/ --connect-to example.com:80:$HOST_IP:80
Response from pod httpbin-5f69c44674-hrvqp
curl http://example.com/ --connect-to example.com:80:$HOST_IP:80
Response from pod httpbin-5f69c44674-hrvqp
```
