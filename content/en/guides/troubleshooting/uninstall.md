---
title: "Uninstall"
description: "Troubleshooting FSM uninstall"
aliases: ["/docs/troubleshooting/uninstall"]
type: docs
weight: 20
---

If for any reason, `fsm uninstall mesh` (as documented in the [uninstall guide](/guides/uninstall/)) fails, you may manually delete FSM resources as detailed below.

Set environment variables for your mesh:
```console
export fsm_namespace=fsm-system # Replace fsm-system with the namespace where FSM is installed
export mesh_name=fsm # Replace fsm with the FSM mesh name
export fsm_version=<fsm version>
export fsm_ca_bundle=<fsm ca bundle>
```

Delete FSM control plane deployments:
```console
kubectl delete deployment -n $fsm_namespace fsm-bootstrap
kubectl delete deployment -n $fsm_namespace fsm-controller
kubectl delete deployment -n $fsm_namespace fsm-injector
```

If FSM was installed alongside Prometheus, Grafana, or Jaeger, delete those deployments:
```console
kubectl delete deployment -n $fsm_namespace fsm-prometheus
kubectl delete deployment -n $fsm_namespace fsm-grafana
kubectl delete deployment -n $fsm_namespace jaeger
```

If FSM was installed with the FSM Multicluster Gateway, delete it by running the following:
```console
kubectl delete deployment -n $fsm_namespace fsm-multicluster-gateway
```

Delete FSM secrets, the meshconfig, and webhook configurations:
> Warning: Ensure that no resources in the cluster depend on the following resources before proceeding.
```console
kubectl delete secret -n $fsm_namespace $fsm_ca_bundle mutating-webhook-cert-secret validating-webhook-cert-secret crd-converter-cert-secret
kubectl delete meshconfig -n $fsm_namespace fsm-mesh-config
kubectl delete mutatingwebhookconfiguration -l app.kubernetes.io/name=openservicemesh.io,app.kubernetes.io/instance=$mesh_name,app.kubernetes.io/version=$fsm_version,app=fsm-injector
kubectl delete validatingwebhookconfiguration -l app.kubernetes.io/name=openservicemesh.io,app.kubernetes.io/instance=mesh_name,app.kubernetes.io/version=$fsm_version,app=fsm-controller
```

To delete FSM and SMI CRDs from the cluster, run the following.
> Warning: Deletion of a CRD will cause all custom resources corresponding to that CRD to also be deleted.
```console
kubectl delete crd meshconfigs.config.openservicemesh.io
kubectl delete crd multiclusterservices.config.openservicemesh.io
kubectl delete crd egresses.policy.openservicemesh.io
kubectl delete crd ingressbackends.policy.openservicemesh.io
kubectl delete crd httproutegroups.specs.smi-spec.io
kubectl delete crd tcproutes.specs.smi-spec.io
kubectl delete crd traffictargets.access.smi-spec.io
kubectl delete crd trafficsplits.split.smi-spec.io
```