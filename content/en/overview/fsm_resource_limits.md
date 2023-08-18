---
title: "FSM Resource Requests and Limits"
description: "Resource requests and limits for FSM pods and deployments"
type: docs
weight: 3
---

| Key                         | Type   | Default                                                                             | Description                                                                                                                      |
| --------------------------- | ------ | ----------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------- |
| fsm.injector.resource       | object | `{"limits":{"cpu":"0.5","memory":"64M"},"requests":{"cpu":"0.3","memory":"64M"}}`   | Sidecar injector's container resource parameters                                                                                 |
| fsm.fsmBootstrap.resource   | object | `{"limits":{"cpu":"0.5","memory":"128M"},"requests":{"cpu":"0.3","memory":"128M"}}` | FSM bootstrap's container resource parameters                                                                                    |
| fsm.fsmController.resource  | object | `{"limits":{"cpu":"1.5","memory":"1G"},"requests":{"cpu":"0.5","memory":"128M"}}`   | FSM controller's container resource parameters. See [Performance and Scalability](/guides/ha_scale/perf-scale) for more details. |
| fsm.cloudConnector.resource | object | `{"limits":{"cpu":"0.5","memory":"64M"},"requests":{"cpu":"0.3","memory":"64M"}}`   | FSM cloud connector's container resource parameter                                                                               |
| fsm.fsmInterceptor.resource | object | `{"limits":{"cpu":"1.5","memory":"1G"},"requests":{"cpu":"0.5","memory":"256M"}}`   | FSM Interceptor's container resource parameters                                                                                  |
| fsm.fsmIngress.resources    | object | `{"limits":{"cpu":"2","memory":"1G"},"requests":{"cpu":"0.5","memory":"128M"}}`     | FSM Ingress's container resource parameters                                                                                      |
| fsm.prometheus.resources    | object | `{"limits":{"cpu":"1","memory":"2G"},"requests":{"cpu":"0.5","memory":"512M"}}`     | Prometheus's container resource parameters                                                                                       |

> Note: These are the default values and can be configured in [values.yaml](https://github.com/flomesh-io/fsm/blob/{{< param fsm_branch >}}/charts/fsm/values.yaml)
