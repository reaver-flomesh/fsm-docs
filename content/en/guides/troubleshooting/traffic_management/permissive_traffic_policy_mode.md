---
title: "Permissive Traffic Policy Mode"
description: "Troubleshooting permissive traffic policy"
type: docs
weight: 2
---

## When permissive traffic policy mode is not working as expected

### 1. Confirm permissive traffic policy mode is enabled

Confirm permissive traffic policy mode is enabled by verifying the value for the `enablePermissiveTrafficPolicyMode` key in the `fsm-mesh-config` custom resource. `fsm-mesh-config` MeshConfig resides in the namespace FSM control plane namespace (`fsm-system` by default).

```console
# Returns true if permissive traffic policy mode is enabled
kubectl get meshconfig fsm-mesh-config -n fsm-system -o jsonpath='{.spec.traffic.enablePermissiveTrafficPolicyMode}{"\n"}'
true
```

The above command must return a boolean string (`true` or `false`) indicating if permissive traffic policy mode is enabled.

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

Use the `fsm verify connectivity` command to validate that the pods can communicate using a Kubernetes service.

For example, to verify if the pod `curl-7bb5845476-zwxbt` in the namespace `curl` can direct traffic to the pod `httpbin-69dc7d545c-n7pjb` in the `httpbin` namespace using the `httpbin` Kubernetes service:

```console
fsm verify connectivity --from-pod curl/curl-7bb5845476-zwxbt --to-pod httpbin/httpbin-69dc7d545c-n7pjb --to-service httpbin
---------------------------------------------
[+] Context: Verify if pod "curl/curl-7bb5845476-zwxbt" can access pod "httpbin/httpbin-69dc7d545c-n7pjb" for service "httpbin/httpbin"
Status: Success

---------------------------------------------
```

The `Status` field in the output will indicate `Success` when the verification succeeds.