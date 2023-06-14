---
title: Service Mesh Interface (SMI) Support
description: "SMI implementation in FSM"
type: docs
---

## Overview

FSM implements [Service Mesh Interface (SMI)](https://smi-spec.io/) resources. This allows FSM users to have flexible implementations of common service mesh scenarios.

## Supported versions

To find out what versions of SMI resources are supported in your mesh, you can run `fsm mesh list`. Here is an excerpt from sample output:

```
MESH NAME   MESH NAMESPACE   SMI SUPPORTED
fsm         fsm-system       HTTPRouteGroup:v1alpha4,TCPRoute:v1alpha4,TrafficSplit:v1alpha4,TrafficTarget:v1alpha3,TrafficMetrics:v1alpha1
```

The following are the currently supported SMI resources and their versions:

### SMI Specification support

|   Kind    | SMI Resource |         Supported Version          |          Comments          |
| :---------------------------- | - | :--------------------------------: |  :--------------------------------: |
| TrafficTarget  | traffictargets.access.smi-spec.io |  [v1alpha3](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-access/v1alpha3/traffic-access.md)  | |
| HTTPRouteGroup | httproutegroups.specs.smi-spec.io | [v1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#httproutegroup) | |
| TCPRoute | tcproutes.specs.smi-spec.io | [v1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-specs/v1alpha4/traffic-specs.md#tcproute) | |
| UDPRoute | udproutes.specs.smi-spec.io | _not supported_ | |
| TrafficSplit | trafficsplits.split.smi-spec.io | [v1alpha4](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-split/v1alpha4/traffic-split.md) | |
| TrafficMetrics  | \*.metrics.smi-spec.io | [v1alpha1](https://github.com/servicemeshinterface/smi-spec/blob/v0.6.0/apis/traffic-metrics/v1alpha1/traffic-metrics.md) | 🚧 **In Progress** 🚧 |
