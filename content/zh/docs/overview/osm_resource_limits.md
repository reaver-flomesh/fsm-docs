---
title: "FSM 资源需求和限制"
description: "给 FSM Pod 和部署的资源需求与限制"
type: docs
weight: 3
---

| 键名 | 类型 | 默认值 | 描述 |
|-----|------|---------|-------------|
| fsm.injector.resource | object | `{"limits":{"cpu":"0.5","memory":"64M"},"requests":{"cpu":"0.3","memory":"64M"}}` | Sidecar 注入器的容器资源参数。|
| fsm.fsmBootstrap.resource | object | `{"limits":{"cpu":"0.5","memory":"128M"},"requests":{"cpu":"0.3","memory":"128M"}}` | FSM 引导程序的容器资源参数。|
| fsm.fsmController.resource | object | `{"limits":{"cpu":"1.5","memory":"1G"},"requests":{"cpu":"0.5","memory":"128M"}}` | FSM 控制器的容器资源参数。请参阅 https://FSM -docs.flomesh.io/docs/guides/ha_scale/scale/ 以获取更多细节。|
| fsm.prometheus.resources | object | `{"limits":{"cpu":"1","memory":"2G"},"requests":{"cpu":"0.5","memory":"512M"}}` | Prometheus 的容器资源参数。|

> 注意：这些都是默认值，其可以通过 [values.yaml](https://github.com/flomesh-io/FSM /blob/{{< param fsm_branch >}}/charts/fsm/values.yaml) 来配置。