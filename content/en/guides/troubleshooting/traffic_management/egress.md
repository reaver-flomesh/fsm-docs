---
title: "Egress Troubleshooting"
description: "Egress Troubleshooting Guide"
type: docs
---

## When Egress is not working as expected

### 1. Confirm egress is enabled

Confirm egress is enabled by verifying the value for the `enableEgress` key in the `fsm-mesh-config` `MeshConfig` custom resource. `fsm-mesh-config` resides in the namespace FSM control plane namespace (`fsm-system` by default).

```console
# Returns true if egress is enabled
kubectl get meshconfig fsm-mesh-config -n fsm-system -o jsonpath='{.spec.traffic.enableEgress}{"\n"}'
true
```

The above command must return a boolean string (`true` or `false`) indicating if egress is enabled.

### 2. Inspect FSM controller logs for errors

```bash
# When fsm-controller is deployed in the fsm-system namespace
kubectl logs -n fsm-system $(kubectl get pod -n fsm-system -l app=fsm-controller -o jsonpath='{.items[0].metadata.name}')
```

Errors will be logged with the `level` key in the log message set to `error`:
```console
{"level":"error","component":"...","time":"...","file":"...","message":"..."}
```

### 3. Confirm the Pipy configuration

Check that egress is enabled in the configuration used by the Pod's sidecar.

```json
{
  "Spec": {
    "SidecarLogLevel": "error",
    "Traffic": {
      "EnableEgress": true
    }
  }
}
```
