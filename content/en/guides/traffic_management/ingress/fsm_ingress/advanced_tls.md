---
title: "Advanced TLS"
description: "Guide on configuring FSM Ingress with TLS and its advanced use"
type: docs
weight: 2
---

# FSM Ingress Controller - Advanced TLS

In the document of [FSM Ingress Controller](/guides/traffic_management/ingress/kubernetes_ingress/), we introduced FSM Ingress and some of its basic functinoality. In this part of series, we will continue on where we left and look into advanced TLS features and we can configure FSM Ingress to use them.

Normally, we see below four combinations of communication with upstream services

- Client -> HTTP Ingress -> HTTP Upstream
- Client -> HTTPS Ingress -> HTTP Upstream
- Client -> HTTP Ingress -> HTTPS Upstream
- Client -> HTTPS Ingress -> HTTPS Upstream

Two of the above combinations has been covered in basics introduction blog post and in this article we will introduce the remaining two combinations i.e. communicating with an upstream HTTPS service.

- HTTPS Upstream: The certificate of the backend service, the upstream, must be checked.
- Client Verification: Mainly when using HTTPS entrance, the certificate used by the client is checked.


![fsm-demo-https-upstream](/images/ingress/tls/fsm-demo-https-upstream.png)

## Demo

- [FSM Ingress Controller Advanced TLS features](/demos/ingress/ingress_tls)