---
title: "TLS Passthrough"
description: "Guide on configuring TLS offloading/termination, passthrough on FSM Ingress"
type: docs
weight: 5
---

# FSM Ingress Controller - TLS Passthrough

This guide will demonstrate TLS passthrough feature of FSM Ingress.


## What is TLS passthrough

TLS (Secure Socket Layer), also known as TLS (Transport Layer Security), protects the security communication between the client and the server through encryption.

![ingress-tls-passthrough](/images/ingress/passthrough/tls-passthrough.png)

TLS Passthrough is one of the two ways that a proxy server handles TLS requests (the other is TLS offload). In TLS passthrough mode, the proxy does not decrypt the TLS request from the client but instead forwards it to the upstream server for decryption, meaning the data remains encrypted while passing through the proxy, thus ensuring the security of important and sensitive data.

## Advantages of TLS passthrough

* Since the data is not decrypted on the proxy but is forwarded to the upstream server in an encrypted manner, the data is protected from network attacks.
* Encrypted data arrives at the upstream server without decryption, ensuring the confidentiality of the data.
* This is also the simplest method of configuring TLS for the proxy.

## Disadvantages of TLS passthrough

* Malicious code may be present in the traffic, which will directly reach the backend server.
* In the TLS passthrough process, switching servers is not possible.
* Layer-7 traffic processing cannot be performed.



## Demo

- [How to use TLS passthrough feature of FSM Ingress](/demos/ingress/ingress_passthrough)
