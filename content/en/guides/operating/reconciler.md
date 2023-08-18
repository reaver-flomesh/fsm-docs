---
title: "Reconciler Guide"
description: "Reconciler Guide"
aliases: ["/docs/reconciler_guide"]
type: docs
weight: 8
---

This guide describes how to enable the reconciler in FSM.

## How the reconciler works

The goal of building a reconciler in FSM is to ensure resources required for the correct operation of FSM's control plane are in their desired state at all times. Resources that are installed as a part of FSM install and have the labels `flomesh.io/reconcile: true` and `app.kubernetes.io/name: flomesh.io` will be reconciled by the reconciler.

**Note**: The reconciler will not operate as desired if the lables `flomesh.io/reconcile: true` and `app.kubernetes.io/name: flomesh.io` are modified or deleted on the reconcilable resources.

An update or delete event on the reconcilable resources will trigger the reconciler and it will reconcile the resource back to its desired state. Only metadata changes (excluding a name change) will be permitted on the reconcilable resources.

### Resources reconciled

The resources that FSM reconciles are:

- CRDs : The CRDs installed/required by FSM [CRDs for FSM](https://github.com/flomesh-io/fsm/tree/{{< param fsm_branch >}}/cmd/fsm-bootstrap/crds) will be reconciled. Since FSM manages the installation and upgrade of the CRDs it needs, FSM will also reconcile them to ensure that their spec, stored and served verions are always in the state that is required by FSM.

- MutatingWebhookConfiguration : A MutatingWebhookConfiguration is deployed as a part of FSM's control plane to enable automatic sidecar injection. As this is a very critical component for pods joining the mesh, FSM reconciles this resource.

- ValidatingWebhookConfiguration : A ValidatingWebhookConfiguration is deployed as a part of FSM's control plane to validate various mesh configurations. This resources validates configurations being applied to the mesh, hence FSM will reconcile this resource.


## How to install FSM with the reconciler

To install FSM with the reconciler, use the below command:

```console
fsm install --set fsm.enableReconciler=true
FSM installed successfully in namespace [fsm-system] with mesh name [fsm]
```

