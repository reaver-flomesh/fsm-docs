---
title: "Canary Rollouts using SMI Traffic Split"
description: "Managing Canary rollouts using SMI Taffic Split"
type: docs
weight: 21
---

This guide demonstrates how to perform Canary rollouts using the SMI Traffic Split configuration.

## Prerequisites

- Kubernetes cluster running Kubernetes {{< param min_k8s_version >}} or greater.
- Have FSM installed.
- Have `kubectl` available to interact with the API server.
- Have `fsm` CLI available for managing the service mesh.

## Demonstration

### Explanation

In this demo, we use two applications, *curl* and *httpbin* implemented with [Pipy](https://github.com/flomesh-io/pipy), to act as client and server respectively. The service has two versions, v1 and v2, which are simulated by deploying *httpbin-v1* and *httpbin-v2*.

> Observant viewers may notice the frequent use of Pipy to implement httpbin functionalities in demonstrations. This is because web services implemented with Pipy can easily customize response content, facilitating the observation of test results.

### Prerequisites

- Kubernetes cluster
- kubectl CLI

### Installing the Service Mesh

Download the FSM CLI.

```shell
system=$(uname -s | tr '[:upper:]' '[:lower:]')
arch=$(uname -m | sed -E 's/x86_/amd/' | sed -E 's/aarch/arm/')
release={{< param fsm_version >}}
curl -L https://github.com/flomesh-io/fsm/releases/download/${release}/fsm-${release}-${system}-${arch}.tar.gz | tar -vxzf -
cp ./${system}-amd64/fsm /usr/local/bin/fsm
```

Install the service mesh and wait for all components to run successfully.

```shell
fsm install --timeout 120s
```

### Deploying the Sample Application

The *curl* and *httpbin* applications run in their respective namespaces, which are managed by the service mesh through the `fsm namespace add xxx` command.

```shell
kubectl create ns httpbin
kubectl create ns curl
fsm namespace add httpbin curl
```

Deploy the v1 version of *httpbin* which returns `Hi, I am v1!` for all HTTP requests. Other applications access *httpbin* through the Service `httpbin`.

```shell
kubectl apply -n httpbin -f - <<EOF
apiVersion: v1
kind: Service
metadata:
  name: httpbin
spec:
  ports:
    - name: pipy
      port: 8080
      targetPort: 8080
      protocol: TCP
  selector:
    app: pipy
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: httpbin-v1
  labels:
    app: pipy
    version: v1
spec:
  replicas: 1
  selector:
    matchLabels:
      app: pipy
      version: v1
  template:
    metadata:
      labels:
        app: pipy
        version: v1
    spec:
      containers:
        - name: pipy
          image: flomesh/pipy:latest
          ports:
            - name: pipy
              containerPort: 8080
          command:
            - pipy
            - -e
            - |
              pipy()
              .listen(8080)
              .serveHTTP(new Message('Hi, I am v1!\n'))
EOF
```

Deploy the *curl* application.

```shell
kubectl apply -n curl -f - <<EOF
apiVersion: v1
kind: Service
metadata:
  name: curl
  labels:
    app: curl
    service: curl
spec:
  ports:
    - name: http
      port: 80
  selector:
    app: curl
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: curl
spec:
  replicas: 1
  selector:
    matchLabels:
      app: curl
  template:
    metadata:
      labels:
        app: curl
    spec:
      containers:
      - image: curlimages/curl
        imagePullPolicy: IfNotPresent
        name: curl
        command: ["sleep", "365d"]
EOF
```

Wait for all applications to run successfully.

```shell
kubectl wait --for=condition=ready pod --all -A
```

Test application access.

```shell
curl_client="$(kubectl get pod -n curl -l app=curl -o jsonpath='{.items[0].metadata.name}')"
# send four request
kubectl exec "$curl_client" -n curl -c curl -- curl -s httpbin.httpbin:8080 httpbin.httpbin:8080 httpbin.httpbin:8080 httpbin.httpbin:8080
```

Expected results are displayed.

```shell
Hi, I am v1!
Hi, I am v1!
Hi, I am v1!
Hi, I am v1!
```

Next, deploy the v2 version of *httpbin*.

### Deploying Version v2

The v2 version of *httpbin* returns `Hi, I am v2!` for all HTTP requests. Before deployment, we need to set the default traffic split strategy, otherwise, the new version instances would be accessible through the Service `httpbin`.

Create Service `httpbin-v1` with an additional `version` tag in its selector compared to Service `httpbin`. Currently, both have the same endpoints.

```shell
kubectl apply -n httpbin -f - <<EOF
apiVersion: v1
kind: Service
metadata:
  name: httpbin-v1
spec:
  ports:
    - name: pipy
      port: 8080
      targetPort: 8080
      protocol: TCP
  selector:
    app: pipy
    version: v1
EOF
```

Apply the `TrafficSplit` strategy to route all traffic to `httpbin-v1`.

```shell
kubectl apply -n httpbin -f - <<EOF
apiVersion: split.smi-spec.io/v1alpha4
kind: TrafficSplit
metadata:
  name: httpbin-split
spec:
  service: httpbin
  backends:
  - service: httpbin-v1
    weight: 100
EOF
```

Then deploy the new version.

```shell
kubectl apply -n httpbin -f - <<EOF
apiVersion: v1
kind: Service
metadata:
  name: httpbin-v2
spec:
  ports:
    - name: pipy
      port: 8080
      targetPort: 8080
      protocol: TCP
  selector:
    app: pipy
    version: v2
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: httpbin-v2
  labels:
    app: pipy
    version: v2
spec:
  replicas: 1
  selector:
    matchLabels:
      app: pipy
      version: v2
  template:
    metadata:
      labels:
        app: pipy
        version: v2
    spec:
      containers:
        - name: pipy
          image: flomesh/pipy:latest
          ports:
            - name: pipy
              containerPort: 8080
          command:
            - pipy
            - -e
            - |
              pipy()
              .listen(8080)
              .serveHTTP(new Message('Hi, I am v2!\n'))
EOF
```

Wait for the new version to run successfully.

```shell
kubectl wait --for=condition=ready pod -n httpbin -l version=v2
```

If you send requests again, v1 *httpbin* still handles them.

```shell
kubectl exec "$curl_client" -n curl -c curl -- curl -s httpbin.httpbin:8080 httpbin.httpbin:8080 httpbin.httpbin:8080 httpbin.httpbin:8080
Hi, I am v1!
Hi, I am v1!
Hi, I am v1!
Hi, I am v1!
```

### Canary Release

Modify the traffic split strategy to direct 25% of the traffic to version v2.

```shell
kubectl apply -n httpbin -f - <<EOF
apiVersion: split.smi-spec.io/v1alpha4
kind: TrafficSplit
metadata:
  name: httpbin-split
spec:
  service: httpbin
  backends:
  - service: httpbin-v1
    weight: 75
  - service: httpbin-v2
    weight: 25
EOF
```

Sending requests again shows that 1/4 of the traffic is handled by version v2.

```shell
kubectl exec "$curl_client" -n curl -c curl -- curl -s httpbin.httpbin:8080 httpbin.httpbin:8080 httpbin.httpbin:8080 httpbin.httpbin:8080
Hi, I am v1!
Hi, I am v2!
Hi, I am v1!
Hi, I am v1!
```

### Advanced Canary Release

Suppose in version v2, `/test` endpoint functionality has been updated. For risk control, we want only a portion of the traffic to access the `/test` endpoint of the v2 version, while other endpoints like `/demo` should only access the v1 version.

We need to introduce another resource to define the traffic visiting the `/test` endpoint: a route definition. Here we define two routes:

- `httpbin-test`: Traffic using the `GET` method to access `/test`.
- `httpbin-all`: Traffic using the `GET` method to access `/`, note this is a prefix match.

```shell
kubectl apply -n httpbin -f - <<EOF
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: httpbin-test
spec:
  matches:
  - name: test
    pathRegex: "/test"
    methods:
    - GET
---
apiVersion: specs.smi-spec.io/v1alpha4
kind: HTTPRouteGroup
metadata:
  name: httpbin-all
spec:
  matches:
  - name: test
    pathRegex: ".*"
    methods:
    - GET
EOF
```

Then

 update the traffic split strategy, associating the routes to the split. Also, create a new policy.

```shell
kubectl apply -n httpbin -f - <<EOF
apiVersion: split.smi-spec.io/v1alpha4
kind: TrafficSplit
metadata:
  name: httpbin-split
spec:
  service: httpbin
  matches:
  - name: httpbin-test
    kind: HTTPRouteGroup
  backends:
  - service: httpbin-v1
    weight: 75
  - service: httpbin-v2
    weight: 25
---
apiVersion: split.smi-spec.io/v1alpha4
kind: TrafficSplit
metadata:
  name: httpbin-all
spec:
  service: httpbin
  matches:
  - name: httpbin-all
    kind: HTTPRouteGroup
  backends:
  - service: httpbin-v1
    weight: 100
EOF
```

Now, when accessing the `/test` endpoint, only 25% of the traffic goes to the new version.

```shell
kubectl exec "$curl_client" -n curl -c curl -- curl -s httpbin.httpbin:8080/test httpbin.httpbin:8080/test httpbin.httpbin:8080/test httpbin.httpbin:8080/test
Hi, I am v1!
Hi, I am v2!
Hi, I am v1!
Hi, I am v1!
```

And requests to the `/demo` endpoint all go to the old v1 version.

```shell
kubectl exec "$curl_client" -n curl -c curl -- curl -s httpbin.httpbin:8080/demo httpbin.httpbin:8080/demo httpbin.httpbin:8080/demo httpbin.httpbin:8080/demo
Hi, I am v1!
Hi, I am v1!
Hi, I am v1!
Hi, I am v1!
```

This meets the expectations.