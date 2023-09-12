---
title: FSM Gateway
description: "Kubernetes Gateway API implementation provided by FSM"
type: docs
weight: 10
---

The FSM Gateway serves as an implementation of the [Kubernetes Gateway API](https://gateway-api.sigs.k8s.io), representing one of the various components within the FSM world.

Upon activation of the FSM Gateway, the FSM controller, assuming the position of gateway overseer, diligently monitors both Kubernetes native resources and Gateway API assets. Subsequently, it dynamically furnishes the pertinent configurations to [Pipy](https://github.com/flomesh-io/pipy), functioning as a proxy.

!!!IMAGE HERE

Should you have an interest in the FSM Gateway, the ensuing documentation might prove beneficial.

