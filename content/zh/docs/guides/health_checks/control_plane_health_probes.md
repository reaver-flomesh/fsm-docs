---
title: "FSM 控制平面健康检查"
description: "健康探测工作原理及失败应对"
aliases: "/docs/control_plane_health_probes"
type: "docs"
---

# FSM 控制平面健康探测

FSM 控制平面组件利用健康探测来传递整体状态。健康探测通过 HTTP 端点（Endpoint）实现，使用 HTTP 状态代码表示成功或失败。

Kubernetes 使用这些探针来传递控制平面 Pod 的状态，并自动执行一些行为以提高可用性。关于 Kubernetes 探针的更多细节可以在[这里](https://kubernetes.io/docs/tasks/configure-pod-container/configure-liveness-readiness-startup-probes/)找到。

## 带探针的 FSM 组件

有健康探针的 FSM 控制平面组件如下：

#### fsm-controller

fsm-controller 的 9091 端口有以下 HTTP 端点可用：

- `/health/alive`: HTTP 200 响应代码表示 FSM 的聚合发现服务（ADS）正在运行。无响应则表示该服务尚未运行。

- `/health/ready`: HTTP 200响应代码表明ADS可以接受来自代理的gRPC连接。HTTP 503或无响应表示来自代理的gRPC连接将不会成功。

#### fsm-injector

fsm-injector上有以下HTTP端点，端口为9090:

- `/healthz`: HTTP 200响应代码表明注入器（injector）可以注入代理sidecar容器。无响应则表示该服务没有正常运行。

## 如何验证 FSM 健康状态？

因为 FSM 的 Kubernetes 资源配置了存活和就绪探测，Kubernetes 会自动轮询 fsm-controller 和 fsm-injector Pod 上的健康端点。

当存活探测失败时，Kubernetes 将产生一个事件（通过 `kubectl describe pod <pod name>` 可见）并重新启动 Pod。`kubectl describe` 的输出如下：

```shell
...
Events:
  Type     Reason     Age               From               Message
  ----     ------     ----              ----               -------
  Normal   Scheduled  24s               default-scheduler  Successfully assigned fsm-system/fsm-controller-85fcb445b-fpv8l to fsm-control-plane
  Normal   Pulling    23s               kubelet            Pulling image "flomesh-io/FSM -controller:v1.1.0"
  Normal   Pulled     23s               kubelet            Successfully pulled image "flomesh-io/FSM -controller:v1.1.0" in 562.2444ms
  Normal   Created    1s (x2 over 23s)  kubelet            Created container fsm-controller
  Normal   Started    1s (x2 over 23s)  kubelet            Started container fsm-controller
  Warning  Unhealthy  1s (x3 over 21s)  kubelet            Liveness probe failed: HTTP probe failed with statuscode: 503
  Normal   Killing    1s                kubelet            Container fsm-controller failed liveness probe, will be restarted
```

当就绪探测失败时，Kubernetes将生成一个事件（通过`kubectl describe pod <pod name>`可以看到），并确保服务流量不会路由到这些不健康的POD上。一个准备就绪探测失败的Pod的`kubectl describe`输出如下：

```shell
...
Events:
  Type     Reason     Age               From               Message
  ----     ------     ----              ----               -------
  Normal   Scheduled  36s               default-scheduler  Successfully assigned fsm-system/fsm-controller-5494bcffb6-tn5jv to fsm-control-plane
  Normal   Pulling    36s               kubelet            Pulling image "flomesh-io/FSM -controller:v1.1.0"
  Normal   Pulled     35s               kubelet            Successfully pulled image "flomesh-io/FSM -controller:v1.1.0" in 746.4323ms
  Normal   Created    35s               kubelet            Created container fsm-controller
  Normal   Started    35s               kubelet            Started container fsm-controller
  Warning  Unhealthy  4s (x3 over 24s)  kubelet            Readiness probe failed: HTTP probe failed with statuscode: 503
```

Pod的 "状态 "也可看出其尚未可用，在 "kubectl get pod "输出中可以看到。例如：

```shell
NAME                              READY   STATUS    RESTARTS   AGE
fsm-controller-5494bcffb6-tn5jv   0/1     Running   0          26s
```

Pod 的健康探测也可以通过转发 Pod 的必要端口并使用 `curl` 或任何其他 HTTP 客户端发出请求手动调用。例如，为了验证 fsm-controller 有效性探测，获取 Pod 的名称并转发 9091 端口：

```shell
# Assuming FSM is installed in the fsm-system namespace
kubectl port-forward -n fsm-system $(kubectl get pods -n fsm-system -l app=fsm-controller -o jsonpath='{.items[0].metadata.name}') 9091
```

然后，在一个单独的终端里，可以使用 `curl` 来检查端点。下面是一个健康的 fsm-controller 的例子：

```console
$ curl -i localhost:9091/health/alive
HTTP/1.1 200 OK
Date: Thu, 18 Mar 2021 20:15:29 GMT
Content-Length: 16
Content-Type: text/plain; charset=utf-8

Service is alive
```

## 排错

如果有健康探测持续失败，请执行以下步骤以确定根本原因：

1. 确保不健康的 fsm-controller 或 fsm-injector Pod 没有运行 Pipy sidecar 容器。

    为了验证 fsm-controller Pod 没有运行 Pipy sidecar 容器，请验证该 Pod 的容器镜像中没有一个是 Pipy 镜像。Pipy 镜像的名字里有 "flomesh/pipy"。

    例如，这是一个包含 Pipy 容器的 fsm-controller Pod：

    ```console
    $ # 假设FSM 被安装在fsm-system命名空间里:
    $ kubectl get pod -n fsm-system $(kubectl get pods -n fsm-system -l app=fsm-controller -o jsonpath='{.items[0].metadata.name}') -o jsonpath='{range .spec.containers[*]}{.image}{"\n"}{end}'
    flomesh/FSM -controller:v1.1.0
    flomesh/pipy:{{< param pipy_version >}}
    ```

    要验证 FSM -injector Pod 是否在运行 Pipy sidecar 容器，请确认 Pod 的容器镜像中没有 Pipy 镜像。Pipy 镜像的名字里有 "flomesh/mesh"。

    例如，这是一个包含 Pipy 容器的 FSM -injector Pod：

    ```console
    $ # 假设FSM 被安装在fsm-system命名空间里:
    $ kubectl get pod -n fsm-system $(kubectl get pods -n fsm-system -l app=fsm-injector -o jsonpath='{.items[0].metadata.name}') -o jsonpath='{range .spec.containers[*]}{.image}{"\n"}{end}'
    flomesh-io/FSM -injector:v0.8.0
    flomesh/pipy:{{< param pipy_version >}}
    ```

    如果任何一个 Pod 正在运行 Pipy 容器，它可能已经被这个或另一个 FSM 实例错误注入。对于每个用 `fsm mesh list` 命令找到的网格，验证不健康的 Pod 的 FSM 命名空间是否列在 `fsm namespace list` 输出的任何 FSM 实例中，通过 `fsm namespace list` 命令可以看到这些命名空间包含 `SIDECAR-INJECTION` "enabled" 的标签。

    例如，对于下列所有网格：

    ```console
    $ fsm mesh list
    
    MESH NAME   NAMESPACE      CONTROLLER PODS                  VERSION     SMI SUPPORTED
    fsm         fsm-system     fsm-controller-5494bcffb6-qpjdv  v0.8.0      HTTPRouteGroup:specs.smi-spec.io/v1alpha4,TCPRoute:specs.smi-spec.io/v1alpha4,TrafficSplit:split.smi-spec.io/v1alpha2,TrafficTarget:access.smi-spec.io/v1alpha3
    fsm2        fsm-system-2   fsm-controller-48fd3c810d-sornc  v0.8.0      HTTPRouteGroup:specs.smi-spec.io/v1alpha4,TCPRoute:specs.smi-spec.io/v1alpha4,TrafficSplit:split.smi-spec.io/v1alpha2,TrafficTarget:access.smi-spec.io/v1alpha3
    ```

    注：  `fsm-system` (网格控制平面命名空间) 在命名空间列表里是如何显示:

    ```console
    $ fsm namespace list --mesh-name fsm --fsm-namespace fsm-system
    NAMESPACE    MESH    SIDECAR-INJECTION
    fsm-system   fsm2    enabled
    bookbuyer    fsm2    enabled
    bookstore    fsm2    enabled
    ```

    如果 FSM 命名空间出现在 "fsm namespace list" 命令中，并且启用了 "SIDECAR-INJECTION "， 则将该名称空间从注入边车的网格中移除。对于上面的例子：

    ```console
    $ fsm namespace remove fsm-system --mesh-name fsm2 --fsm-namespace fsm-system2
    ```

2. 确定 Kubernetes 在调度或启动 Pod 时是否遇到错误。

    使用`kubectl describe`命令查找关于不健康的Pod近期错误。

    对于 fsm-controller:

    ```console
    $ # 假设FSM 被安装在fsm-system命名空间里:
    $ kubectl describe pod -n fsm-system $(kubectl get pods -n fsm-system -l app=fsm-controller -o jsonpath='{.items[0].metadata.name}')
    ```

    对于 fsm-injector:

    ```console
    $ # 假设FSM 被安装在fsm-system命名空间里:
    $ kubectl describe pod -n fsm-system $(kubectl get pods -n fsm-system -l app=fsm-injector -o jsonpath='{.items[0].metadata.name}')
    ```

    解决这些错误并再次验证 FSM 的健康状态。

3. 确定 Pod 是否遇到了一个运行时错误。

    通过检查容器的日志，寻找可能在容器启动后发生的任何错误。具体来说，寻找任何包含字符串 `"level": "error"` 的日志。

    对于 fsm-controller:

    ```console
    $ # 假设FSM 被安装在fsm-system命名空间里:
    $ kubectl logs -n fsm-system $(kubectl get pods -n fsm-system -l app=fsm-controller -o jsonpath='{.items[0].metadata.name}')
    ```

    对于 FSM -injector:

    ```console
    $ # Assuming FSM is installed in the fsm-system namespace:
    $ kubectl logs -n fsm-system $(kubectl get pods -n fsm-system -l app=fsm-injector -o jsonpath='{.items[0].metadata.name}')
    ```

    解决这些错误并再次验证 FSM 的健康状态。
