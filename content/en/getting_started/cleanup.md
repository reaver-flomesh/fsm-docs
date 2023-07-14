---
title: "Uninstall FSM"
description: "Uninstall FSM and the bookstore applications"
type: docs
weight: 6
---

The articles in the getting started section outline installing FSM and sample applications. To uninstall all of these resources, delete the sample application and related SMI resources and uninstall the FSM control plane and cluster-wide FSM resources.

## Delete the sample applications

To clean up the sample applications and related SMI resources, delete their namespaces. For example:

```bash
kubectl delete ns bookbuyer bookthief bookstore bookwarehouse
```

## Uninstall FSM control plane

To uninstall FSM control plane, use `fsm unistall mesh`.

```bash
fsm uninstall mesh
```

## Uninstall FSM cluster-wide resources

To uninstall FSM cluster-wide resources, use `fsm uninstall mesh --delete-cluster-wide-resources`.

```bash
fsm uninstall mesh --delete-cluster-wide-resources
```

For more details about uninstalling FSM, see the [uninstallation guide](/guides/operating/uninstall/).
