---
title: "Policy Attachment v1alpha1 API Reference"
description: "Policy  v1alpha1 API reference documentation."
type: docs
---

<p>Packages:</p>
<ul>
<li>
<a href="#gateway.flomesh.io%2fv1alpha1">gateway.flomesh.io/v1alpha1</a>
</li>
</ul>
<h2 id="gateway.flomesh.io/v1alpha1">gateway.flomesh.io/v1alpha1</h2>
<div>
<p>Package v1alpha1 is the v1alpha3 version of the API.</p>
</div>
Resource Types:
<ul><li>
<a href="#gateway.flomesh.io/v1alpha1.AccessControlPolicy">AccessControlPolicy</a>
</li><li>
<a href="#gateway.flomesh.io/v1alpha1.CircuitBreakingPolicy">CircuitBreakingPolicy</a>
</li><li>
<a href="#gateway.flomesh.io/v1alpha1.FaultInjectionPolicy">FaultInjectionPolicy</a>
</li><li>
<a href="#gateway.flomesh.io/v1alpha1.GatewayTLSPolicy">GatewayTLSPolicy</a>
</li><li>
<a href="#gateway.flomesh.io/v1alpha1.HealthCheckPolicy">HealthCheckPolicy</a>
</li><li>
<a href="#gateway.flomesh.io/v1alpha1.LoadBalancerPolicy">LoadBalancerPolicy</a>
</li><li>
<a href="#gateway.flomesh.io/v1alpha1.RateLimitPolicy">RateLimitPolicy</a>
</li><li>
<a href="#gateway.flomesh.io/v1alpha1.RetryPolicy">RetryPolicy</a>
</li><li>
<a href="#gateway.flomesh.io/v1alpha1.SessionStickyPolicy">SessionStickyPolicy</a>
</li><li>
<a href="#gateway.flomesh.io/v1alpha1.UpstreamTLSPolicy">UpstreamTLSPolicy</a>
</li></ul>
<h3 id="gateway.flomesh.io/v1alpha1.AccessControlPolicy">AccessControlPolicy
</h3>
<div>
<p>AccessControlPolicy is the Schema for the AccessControlPolicy API</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>apiVersion</code><br/>
string</td>
<td>
<code>
gateway.flomesh.io/v1alpha1
</code>
</td>
</tr>
<tr>
<td>
<code>kind</code><br/>
string
</td>
<td><code>AccessControlPolicy</code></td>
</tr>
<tr>
<td>
<code>metadata</code><br/>
<em>
<a href="https://v1-26.docs.kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#objectmeta-v1-meta">
Kubernetes meta/v1.ObjectMeta
</a>
</em>
</td>
<td>
Refer to the Kubernetes API documentation for the fields of the
<code>metadata</code> field.
</td>
</tr>
<tr>
<td>
<code>spec</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.AccessControlPolicySpec">
AccessControlPolicySpec
</a>
</em>
</td>
<td>
<br/>
<br/>
<table>
<tr>
<td>
<code>targetRef</code><br/>
<em>
<a href="https://pkg.go.dev/sigs.k8s.io/gateway-api@v0.7.1/apis/v1alpha2#PolicyTargetReference">
sigs.k8s.io/gateway-api/apis/v1alpha2.PolicyTargetReference
</a>
</em>
</td>
<td>
<p>TargetRef is the reference to the target resource to which the policy is applied</p>
</td>
</tr>
<tr>
<td>
<code>ports</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.PortAccessControl">
[]PortAccessControl
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Ports is the access control configuration for ports</p>
</td>
</tr>
<tr>
<td>
<code>hostnames</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.HostnameAccessControl">
[]HostnameAccessControl
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Hostnames is the access control configuration for hostnames</p>
</td>
</tr>
<tr>
<td>
<code>http</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.HTTPAccessControl">
[]HTTPAccessControl
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>HTTPAccessControls is the access control configuration for HTTP routes</p>
</td>
</tr>
<tr>
<td>
<code>grpc</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.GRPCAccessControl">
[]GRPCAccessControl
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>GRPCAccessControls is the access control configuration for GRPC routes</p>
</td>
</tr>
<tr>
<td>
<code>config</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.AccessControlConfig">
AccessControlConfig
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>DefaultConfig is the default access control for all ports, routes and hostnames</p>
</td>
</tr>
</table>
</td>
</tr>
<tr>
<td>
<code>status</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.AccessControlPolicyStatus">
AccessControlPolicyStatus
</a>
</em>
</td>
<td>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.CircuitBreakingPolicy">CircuitBreakingPolicy
</h3>
<div>
<p>CircuitBreakingPolicy is the Schema for the CircuitBreakingPolicy API</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>apiVersion</code><br/>
string</td>
<td>
<code>
gateway.flomesh.io/v1alpha1
</code>
</td>
</tr>
<tr>
<td>
<code>kind</code><br/>
string
</td>
<td><code>CircuitBreakingPolicy</code></td>
</tr>
<tr>
<td>
<code>metadata</code><br/>
<em>
<a href="https://v1-26.docs.kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#objectmeta-v1-meta">
Kubernetes meta/v1.ObjectMeta
</a>
</em>
</td>
<td>
Refer to the Kubernetes API documentation for the fields of the
<code>metadata</code> field.
</td>
</tr>
<tr>
<td>
<code>spec</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.CircuitBreakingPolicySpec">
CircuitBreakingPolicySpec
</a>
</em>
</td>
<td>
<br/>
<br/>
<table>
<tr>
<td>
<code>targetRef</code><br/>
<em>
<a href="https://pkg.go.dev/sigs.k8s.io/gateway-api@v0.7.1/apis/v1alpha2#PolicyTargetReference">
sigs.k8s.io/gateway-api/apis/v1alpha2.PolicyTargetReference
</a>
</em>
</td>
<td>
<p>TargetRef is the reference to the target resource to which the policy is applied</p>
</td>
</tr>
<tr>
<td>
<code>ports</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.PortCircuitBreaking">
[]PortCircuitBreaking
</a>
</em>
</td>
<td>
<p>Ports is the circuit breaking configuration for ports</p>
</td>
</tr>
<tr>
<td>
<code>config</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.CircuitBreakingConfig">
CircuitBreakingConfig
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>DefaultConfig is the default circuit breaking configuration for all ports</p>
</td>
</tr>
</table>
</td>
</tr>
<tr>
<td>
<code>status</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.CircuitBreakingPolicyStatus">
CircuitBreakingPolicyStatus
</a>
</em>
</td>
<td>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.FaultInjectionPolicy">FaultInjectionPolicy
</h3>
<div>
<p>FaultInjectionPolicy is the Schema for the FaultInjectionPolicy API</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>apiVersion</code><br/>
string</td>
<td>
<code>
gateway.flomesh.io/v1alpha1
</code>
</td>
</tr>
<tr>
<td>
<code>kind</code><br/>
string
</td>
<td><code>FaultInjectionPolicy</code></td>
</tr>
<tr>
<td>
<code>metadata</code><br/>
<em>
<a href="https://v1-26.docs.kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#objectmeta-v1-meta">
Kubernetes meta/v1.ObjectMeta
</a>
</em>
</td>
<td>
Refer to the Kubernetes API documentation for the fields of the
<code>metadata</code> field.
</td>
</tr>
<tr>
<td>
<code>spec</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.FaultInjectionPolicySpec">
FaultInjectionPolicySpec
</a>
</em>
</td>
<td>
<br/>
<br/>
<table>
<tr>
<td>
<code>targetRef</code><br/>
<em>
<a href="https://pkg.go.dev/sigs.k8s.io/gateway-api@v0.7.1/apis/v1alpha2#PolicyTargetReference">
sigs.k8s.io/gateway-api/apis/v1alpha2.PolicyTargetReference
</a>
</em>
</td>
<td>
<p>TargetRef is the reference to the target resource to which the policy is applied</p>
</td>
</tr>
<tr>
<td>
<code>hostnames</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.HostnameFaultInjection">
[]HostnameFaultInjection
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Hostnames is the access control configuration for hostnames</p>
</td>
</tr>
<tr>
<td>
<code>http</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.HTTPFaultInjection">
[]HTTPFaultInjection
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>HTTPFaultInjections is the access control configuration for HTTP routes</p>
</td>
</tr>
<tr>
<td>
<code>grpc</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.GRPCFaultInjection">
[]GRPCFaultInjection
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>GRPCFaultInjections is the access control configuration for GRPC routes</p>
</td>
</tr>
<tr>
<td>
<code>config</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.FaultInjectionConfig">
FaultInjectionConfig
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>DefaultConfig is the default access control for all ports, routes and hostnames</p>
</td>
</tr>
<tr>
<td>
<code>unit</code><br/>
<em>
string
</em>
</td>
<td>
<em>(Optional)</em>
<p>Unit is the unit of delay duration, default Unit is ms</p>
</td>
</tr>
</table>
</td>
</tr>
<tr>
<td>
<code>status</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.FaultInjectionPolicyStatus">
FaultInjectionPolicyStatus
</a>
</em>
</td>
<td>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.GatewayTLSPolicy">GatewayTLSPolicy
</h3>
<div>
<p>GatewayTLSPolicy is the Schema for the GatewayTLSPolicy API</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>apiVersion</code><br/>
string</td>
<td>
<code>
gateway.flomesh.io/v1alpha1
</code>
</td>
</tr>
<tr>
<td>
<code>kind</code><br/>
string
</td>
<td><code>GatewayTLSPolicy</code></td>
</tr>
<tr>
<td>
<code>metadata</code><br/>
<em>
<a href="https://v1-26.docs.kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#objectmeta-v1-meta">
Kubernetes meta/v1.ObjectMeta
</a>
</em>
</td>
<td>
Refer to the Kubernetes API documentation for the fields of the
<code>metadata</code> field.
</td>
</tr>
<tr>
<td>
<code>spec</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.GatewayTLSPolicySpec">
GatewayTLSPolicySpec
</a>
</em>
</td>
<td>
<br/>
<br/>
<table>
<tr>
<td>
<code>targetRef</code><br/>
<em>
<a href="https://pkg.go.dev/sigs.k8s.io/gateway-api@v0.7.1/apis/v1alpha2#PolicyTargetReference">
sigs.k8s.io/gateway-api/apis/v1alpha2.PolicyTargetReference
</a>
</em>
</td>
<td>
<p>TargetRef is the reference to the target resource to which the policy is applied</p>
</td>
</tr>
<tr>
<td>
<code>ports</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.PortGatewayTLS">
[]PortGatewayTLS
</a>
</em>
</td>
<td>
<p>Ports is the Gateway TLS configuration for ports</p>
</td>
</tr>
<tr>
<td>
<code>config</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.GatewayTLSConfig">
GatewayTLSConfig
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>DefaultConfig is the default Gateway TLS configuration for all ports</p>
</td>
</tr>
</table>
</td>
</tr>
<tr>
<td>
<code>status</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.GatewayTLSPolicyStatus">
GatewayTLSPolicyStatus
</a>
</em>
</td>
<td>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.HealthCheckPolicy">HealthCheckPolicy
</h3>
<div>
<p>HealthCheckPolicy is the Schema for the HealthCheckPolicy API</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>apiVersion</code><br/>
string</td>
<td>
<code>
gateway.flomesh.io/v1alpha1
</code>
</td>
</tr>
<tr>
<td>
<code>kind</code><br/>
string
</td>
<td><code>HealthCheckPolicy</code></td>
</tr>
<tr>
<td>
<code>metadata</code><br/>
<em>
<a href="https://v1-26.docs.kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#objectmeta-v1-meta">
Kubernetes meta/v1.ObjectMeta
</a>
</em>
</td>
<td>
Refer to the Kubernetes API documentation for the fields of the
<code>metadata</code> field.
</td>
</tr>
<tr>
<td>
<code>spec</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.HealthCheckPolicySpec">
HealthCheckPolicySpec
</a>
</em>
</td>
<td>
<br/>
<br/>
<table>
<tr>
<td>
<code>targetRef</code><br/>
<em>
<a href="https://pkg.go.dev/sigs.k8s.io/gateway-api@v0.7.1/apis/v1alpha2#PolicyTargetReference">
sigs.k8s.io/gateway-api/apis/v1alpha2.PolicyTargetReference
</a>
</em>
</td>
<td>
<p>TargetRef is the reference to the target resource to which the policy is applied</p>
</td>
</tr>
<tr>
<td>
<code>ports</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.PortHealthCheck">
[]PortHealthCheck
</a>
</em>
</td>
<td>
<p>Ports is the health check configuration for ports</p>
</td>
</tr>
<tr>
<td>
<code>config</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.HealthCheckConfig">
HealthCheckConfig
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>DefaultConfig is the default health check configuration for all ports</p>
</td>
</tr>
</table>
</td>
</tr>
<tr>
<td>
<code>status</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.HealthCheckPolicyStatus">
HealthCheckPolicyStatus
</a>
</em>
</td>
<td>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.LoadBalancerPolicy">LoadBalancerPolicy
</h3>
<div>
<p>LoadBalancerPolicy is the Schema for the LoadBalancerPolicy API</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>apiVersion</code><br/>
string</td>
<td>
<code>
gateway.flomesh.io/v1alpha1
</code>
</td>
</tr>
<tr>
<td>
<code>kind</code><br/>
string
</td>
<td><code>LoadBalancerPolicy</code></td>
</tr>
<tr>
<td>
<code>metadata</code><br/>
<em>
<a href="https://v1-26.docs.kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#objectmeta-v1-meta">
Kubernetes meta/v1.ObjectMeta
</a>
</em>
</td>
<td>
Refer to the Kubernetes API documentation for the fields of the
<code>metadata</code> field.
</td>
</tr>
<tr>
<td>
<code>spec</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.LoadBalancerPolicySpec">
LoadBalancerPolicySpec
</a>
</em>
</td>
<td>
<br/>
<br/>
<table>
<tr>
<td>
<code>targetRef</code><br/>
<em>
<a href="https://pkg.go.dev/sigs.k8s.io/gateway-api@v0.7.1/apis/v1alpha2#PolicyTargetReference">
sigs.k8s.io/gateway-api/apis/v1alpha2.PolicyTargetReference
</a>
</em>
</td>
<td>
<p>TargetRef is the reference to the target resource to which the policy is applied</p>
</td>
</tr>
<tr>
<td>
<code>ports</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.PortLoadBalancer">
[]PortLoadBalancer
</a>
</em>
</td>
<td>
<p>Ports is the load balancer configuration for ports</p>
</td>
</tr>
<tr>
<td>
<code>type</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.LoadBalancerType">
LoadBalancerType
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>DefaultType is the default type of the load balancer for all ports</p>
</td>
</tr>
</table>
</td>
</tr>
<tr>
<td>
<code>status</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.LoadBalancerPolicyStatus">
LoadBalancerPolicyStatus
</a>
</em>
</td>
<td>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.RateLimitPolicy">RateLimitPolicy
</h3>
<div>
<p>RateLimitPolicy is the Schema for the RateLimitPolicy API</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>apiVersion</code><br/>
string</td>
<td>
<code>
gateway.flomesh.io/v1alpha1
</code>
</td>
</tr>
<tr>
<td>
<code>kind</code><br/>
string
</td>
<td><code>RateLimitPolicy</code></td>
</tr>
<tr>
<td>
<code>metadata</code><br/>
<em>
<a href="https://v1-26.docs.kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#objectmeta-v1-meta">
Kubernetes meta/v1.ObjectMeta
</a>
</em>
</td>
<td>
Refer to the Kubernetes API documentation for the fields of the
<code>metadata</code> field.
</td>
</tr>
<tr>
<td>
<code>spec</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.RateLimitPolicySpec">
RateLimitPolicySpec
</a>
</em>
</td>
<td>
<br/>
<br/>
<table>
<tr>
<td>
<code>targetRef</code><br/>
<em>
<a href="https://pkg.go.dev/sigs.k8s.io/gateway-api@v0.7.1/apis/v1alpha2#PolicyTargetReference">
sigs.k8s.io/gateway-api/apis/v1alpha2.PolicyTargetReference
</a>
</em>
</td>
<td>
<p>TargetRef is the reference to the target resource to which the policy is applied</p>
</td>
</tr>
<tr>
<td>
<code>ports</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.PortRateLimit">
[]PortRateLimit
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Ports is the rate limit configuration for ports</p>
</td>
</tr>
<tr>
<td>
<code>bps</code><br/>
<em>
int64
</em>
</td>
<td>
<em>(Optional)</em>
<p>DefaultBPS is the default rate limit for all ports</p>
</td>
</tr>
<tr>
<td>
<code>hostnames</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.HostnameRateLimit">
[]HostnameRateLimit
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Hostnames is the rate limit configuration for hostnames</p>
</td>
</tr>
<tr>
<td>
<code>http</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.HTTPRateLimit">
[]HTTPRateLimit
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>HTTPRateLimits is the rate limit configuration for HTTP routes</p>
</td>
</tr>
<tr>
<td>
<code>grpc</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.GRPCRateLimit">
[]GRPCRateLimit
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>GRPCRateLimits is the rate limit configuration for GRPC routes</p>
</td>
</tr>
<tr>
<td>
<code>config</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.L7RateLimit">
L7RateLimit
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>DefaultConfig is the default rate limit for all routes and hostnames</p>
</td>
</tr>
</table>
</td>
</tr>
<tr>
<td>
<code>status</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.RateLimitPolicyStatus">
RateLimitPolicyStatus
</a>
</em>
</td>
<td>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.RetryPolicy">RetryPolicy
</h3>
<div>
<p>RetryPolicy is the Schema for the RetryPolicy API</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>apiVersion</code><br/>
string</td>
<td>
<code>
gateway.flomesh.io/v1alpha1
</code>
</td>
</tr>
<tr>
<td>
<code>kind</code><br/>
string
</td>
<td><code>RetryPolicy</code></td>
</tr>
<tr>
<td>
<code>metadata</code><br/>
<em>
<a href="https://v1-26.docs.kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#objectmeta-v1-meta">
Kubernetes meta/v1.ObjectMeta
</a>
</em>
</td>
<td>
Refer to the Kubernetes API documentation for the fields of the
<code>metadata</code> field.
</td>
</tr>
<tr>
<td>
<code>spec</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.RetryPolicySpec">
RetryPolicySpec
</a>
</em>
</td>
<td>
<br/>
<br/>
<table>
<tr>
<td>
<code>targetRef</code><br/>
<em>
<a href="https://pkg.go.dev/sigs.k8s.io/gateway-api@v0.7.1/apis/v1alpha2#PolicyTargetReference">
sigs.k8s.io/gateway-api/apis/v1alpha2.PolicyTargetReference
</a>
</em>
</td>
<td>
<p>TargetRef is the reference to the target resource to which the policy is applied</p>
</td>
</tr>
<tr>
<td>
<code>ports</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.PortRetry">
[]PortRetry
</a>
</em>
</td>
<td>
<p>Ports is the retry configuration for ports</p>
</td>
</tr>
<tr>
<td>
<code>config</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.RetryConfig">
RetryConfig
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>DefaultConfig is the default retry configuration for all ports</p>
</td>
</tr>
</table>
</td>
</tr>
<tr>
<td>
<code>status</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.RetryPolicyStatus">
RetryPolicyStatus
</a>
</em>
</td>
<td>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.SessionStickyPolicy">SessionStickyPolicy
</h3>
<div>
<p>SessionStickyPolicy is the Schema for the SessionStickyPolicy API</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>apiVersion</code><br/>
string</td>
<td>
<code>
gateway.flomesh.io/v1alpha1
</code>
</td>
</tr>
<tr>
<td>
<code>kind</code><br/>
string
</td>
<td><code>SessionStickyPolicy</code></td>
</tr>
<tr>
<td>
<code>metadata</code><br/>
<em>
<a href="https://v1-26.docs.kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#objectmeta-v1-meta">
Kubernetes meta/v1.ObjectMeta
</a>
</em>
</td>
<td>
Refer to the Kubernetes API documentation for the fields of the
<code>metadata</code> field.
</td>
</tr>
<tr>
<td>
<code>spec</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.SessionStickyPolicySpec">
SessionStickyPolicySpec
</a>
</em>
</td>
<td>
<br/>
<br/>
<table>
<tr>
<td>
<code>targetRef</code><br/>
<em>
<a href="https://pkg.go.dev/sigs.k8s.io/gateway-api@v0.7.1/apis/v1alpha2#PolicyTargetReference">
sigs.k8s.io/gateway-api/apis/v1alpha2.PolicyTargetReference
</a>
</em>
</td>
<td>
<p>TargetRef is the reference to the target resource to which the policy is applied</p>
</td>
</tr>
<tr>
<td>
<code>ports</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.PortSessionSticky">
[]PortSessionSticky
</a>
</em>
</td>
<td>
<p>Ports is the session sticky configuration for ports</p>
</td>
</tr>
<tr>
<td>
<code>config</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.SessionStickyConfig">
SessionStickyConfig
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>DefaultConfig is the default session sticky configuration for all ports</p>
</td>
</tr>
</table>
</td>
</tr>
<tr>
<td>
<code>status</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.SessionStickyPolicyStatus">
SessionStickyPolicyStatus
</a>
</em>
</td>
<td>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.UpstreamTLSPolicy">UpstreamTLSPolicy
</h3>
<div>
<p>UpstreamTLSPolicy is the Schema for the UpstreamTLSPolicy API</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>apiVersion</code><br/>
string</td>
<td>
<code>
gateway.flomesh.io/v1alpha1
</code>
</td>
</tr>
<tr>
<td>
<code>kind</code><br/>
string
</td>
<td><code>UpstreamTLSPolicy</code></td>
</tr>
<tr>
<td>
<code>metadata</code><br/>
<em>
<a href="https://v1-26.docs.kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#objectmeta-v1-meta">
Kubernetes meta/v1.ObjectMeta
</a>
</em>
</td>
<td>
Refer to the Kubernetes API documentation for the fields of the
<code>metadata</code> field.
</td>
</tr>
<tr>
<td>
<code>spec</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.UpstreamTLSPolicySpec">
UpstreamTLSPolicySpec
</a>
</em>
</td>
<td>
<br/>
<br/>
<table>
<tr>
<td>
<code>targetRef</code><br/>
<em>
<a href="https://pkg.go.dev/sigs.k8s.io/gateway-api@v0.7.1/apis/v1alpha2#PolicyTargetReference">
sigs.k8s.io/gateway-api/apis/v1alpha2.PolicyTargetReference
</a>
</em>
</td>
<td>
<p>TargetRef is the reference to the target resource to which the policy is applied</p>
</td>
</tr>
<tr>
<td>
<code>ports</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.PortUpstreamTLS">
[]PortUpstreamTLS
</a>
</em>
</td>
<td>
<p>Ports is the session sticky configuration for ports</p>
</td>
</tr>
<tr>
<td>
<code>config</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.UpstreamTLSConfig">
UpstreamTLSConfig
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>DefaultConfig is the default session sticky configuration for all ports</p>
</td>
</tr>
</table>
</td>
</tr>
<tr>
<td>
<code>status</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.UpstreamTLSPolicyStatus">
UpstreamTLSPolicyStatus
</a>
</em>
</td>
<td>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.AccessControlConfig">AccessControlConfig
</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.AccessControlPolicySpec">AccessControlPolicySpec</a>, <a href="#gateway.flomesh.io/v1alpha1.GRPCAccessControl">GRPCAccessControl</a>, <a href="#gateway.flomesh.io/v1alpha1.HTTPAccessControl">HTTPAccessControl</a>, <a href="#gateway.flomesh.io/v1alpha1.HostnameAccessControl">HostnameAccessControl</a>, <a href="#gateway.flomesh.io/v1alpha1.PortAccessControl">PortAccessControl</a>)
</p>
<div>
<p>AccessControlConfig defines the access control configuration for a route</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>blacklist</code><br/>
<em>
[]string
</em>
</td>
<td>
<em>(Optional)</em>
<p>Blacklist is the list of IP addresses to be blacklisted</p>
</td>
</tr>
<tr>
<td>
<code>whitelist</code><br/>
<em>
[]string
</em>
</td>
<td>
<em>(Optional)</em>
<p>Whitelist is the list of IP addresses to be whitelisted</p>
</td>
</tr>
<tr>
<td>
<code>enableXFF</code><br/>
<em>
bool
</em>
</td>
<td>
<em>(Optional)</em>
<p>EnableXFF is the flag to enable X-Forwarded-For header</p>
</td>
</tr>
<tr>
<td>
<code>statusCode</code><br/>
<em>
int32
</em>
</td>
<td>
<em>(Optional)</em>
<p>StatusCode is the response status code to be returned when the access control is exceeded</p>
</td>
</tr>
<tr>
<td>
<code>message</code><br/>
<em>
string
</em>
</td>
<td>
<em>(Optional)</em>
<p>Message is the response message to be returned when the access control is exceeded</p>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.AccessControlPolicySpec">AccessControlPolicySpec
</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.AccessControlPolicy">AccessControlPolicy</a>)
</p>
<div>
<p>AccessControlPolicySpec defines the desired state of AccessControlPolicy</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>targetRef</code><br/>
<em>
<a href="https://pkg.go.dev/sigs.k8s.io/gateway-api@v0.7.1/apis/v1alpha2#PolicyTargetReference">
sigs.k8s.io/gateway-api/apis/v1alpha2.PolicyTargetReference
</a>
</em>
</td>
<td>
<p>TargetRef is the reference to the target resource to which the policy is applied</p>
</td>
</tr>
<tr>
<td>
<code>ports</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.PortAccessControl">
[]PortAccessControl
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Ports is the access control configuration for ports</p>
</td>
</tr>
<tr>
<td>
<code>hostnames</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.HostnameAccessControl">
[]HostnameAccessControl
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Hostnames is the access control configuration for hostnames</p>
</td>
</tr>
<tr>
<td>
<code>http</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.HTTPAccessControl">
[]HTTPAccessControl
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>HTTPAccessControls is the access control configuration for HTTP routes</p>
</td>
</tr>
<tr>
<td>
<code>grpc</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.GRPCAccessControl">
[]GRPCAccessControl
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>GRPCAccessControls is the access control configuration for GRPC routes</p>
</td>
</tr>
<tr>
<td>
<code>config</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.AccessControlConfig">
AccessControlConfig
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>DefaultConfig is the default access control for all ports, routes and hostnames</p>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.AccessControlPolicyStatus">AccessControlPolicyStatus
</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.AccessControlPolicy">AccessControlPolicy</a>)
</p>
<div>
<p>AccessControlPolicyStatus defines the observed state of AccessControlPolicy</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>conditions</code><br/>
<em>
<a href="https://v1-26.docs.kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#condition-v1-meta">
[]Kubernetes meta/v1.Condition
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Conditions describe the current conditions of the AccessControlPolicy.</p>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.CircuitBreakingConfig">CircuitBreakingConfig
</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.CircuitBreakingPolicySpec">CircuitBreakingPolicySpec</a>, <a href="#gateway.flomesh.io/v1alpha1.PortCircuitBreaking">PortCircuitBreaking</a>)
</p>
<div>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>minRequestAmount</code><br/>
<em>
int32
</em>
</td>
<td>
<p>MinRequestAmount is the minimum number of requests in the StatTimeWindow</p>
</td>
</tr>
<tr>
<td>
<code>statTimeWindow</code><br/>
<em>
int32
</em>
</td>
<td>
<p>StatTimeWindow is the time window in seconds to collect statistics</p>
</td>
</tr>
<tr>
<td>
<code>slowTimeThreshold</code><br/>
<em>
float32
</em>
</td>
<td>
<em>(Optional)</em>
<p>SlowTimeThreshold is the threshold in seconds to determine a slow request</p>
</td>
</tr>
<tr>
<td>
<code>slowAmountThreshold</code><br/>
<em>
int32
</em>
</td>
<td>
<em>(Optional)</em>
<p>SlowAmountThreshold is the threshold of slow requests in the StatTimeWindow to trigger circuit breaking</p>
</td>
</tr>
<tr>
<td>
<code>slowRatioThreshold</code><br/>
<em>
float32
</em>
</td>
<td>
<em>(Optional)</em>
<p>SlowRatioThreshold is the threshold of slow requests ratio in the StatTimeWindow to trigger circuit breaking</p>
</td>
</tr>
<tr>
<td>
<code>errorAmountThreshold</code><br/>
<em>
int32
</em>
</td>
<td>
<em>(Optional)</em>
<p>ErrorAmountThreshold is the threshold of error requests in the StatTimeWindow to trigger circuit breaking</p>
</td>
</tr>
<tr>
<td>
<code>errorRatioThreshold</code><br/>
<em>
float32
</em>
</td>
<td>
<em>(Optional)</em>
<p>ErrorRatioThreshold is the threshold of error requests ratio in the StatTimeWindow to trigger circuit breaking</p>
</td>
</tr>
<tr>
<td>
<code>degradedTimeWindow</code><br/>
<em>
int32
</em>
</td>
<td>
<p>DegradedTimeWindow is the time window in seconds to degrade the service</p>
</td>
</tr>
<tr>
<td>
<code>degradedStatusCode</code><br/>
<em>
int32
</em>
</td>
<td>
<p>DegradedStatusCode is the status code to return when the service is degraded</p>
</td>
</tr>
<tr>
<td>
<code>degradedResponseContent</code><br/>
<em>
string
</em>
</td>
<td>
<em>(Optional)</em>
<p>DegradedResponseContent is the response content to return when the service is degraded</p>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.CircuitBreakingPolicySpec">CircuitBreakingPolicySpec
</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.CircuitBreakingPolicy">CircuitBreakingPolicy</a>)
</p>
<div>
<p>CircuitBreakingPolicySpec defines the desired state of CircuitBreakingPolicy</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>targetRef</code><br/>
<em>
<a href="https://pkg.go.dev/sigs.k8s.io/gateway-api@v0.7.1/apis/v1alpha2#PolicyTargetReference">
sigs.k8s.io/gateway-api/apis/v1alpha2.PolicyTargetReference
</a>
</em>
</td>
<td>
<p>TargetRef is the reference to the target resource to which the policy is applied</p>
</td>
</tr>
<tr>
<td>
<code>ports</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.PortCircuitBreaking">
[]PortCircuitBreaking
</a>
</em>
</td>
<td>
<p>Ports is the circuit breaking configuration for ports</p>
</td>
</tr>
<tr>
<td>
<code>config</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.CircuitBreakingConfig">
CircuitBreakingConfig
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>DefaultConfig is the default circuit breaking configuration for all ports</p>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.CircuitBreakingPolicyStatus">CircuitBreakingPolicyStatus
</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.CircuitBreakingPolicy">CircuitBreakingPolicy</a>)
</p>
<div>
<p>CircuitBreakingPolicyStatus defines the observed state of CircuitBreakingPolicy</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>conditions</code><br/>
<em>
<a href="https://v1-26.docs.kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#condition-v1-meta">
[]Kubernetes meta/v1.Condition
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Conditions describe the current conditions of the CircuitBreakingPolicy.</p>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.FaultInjectionAbort">FaultInjectionAbort
</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.FaultInjectionConfig">FaultInjectionConfig</a>)
</p>
<div>
<p>FaultInjectionAbort defines the abort configuration</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>percent</code><br/>
<em>
int32
</em>
</td>
<td>
<p>Percent is the percentage of requests to abort</p>
</td>
</tr>
<tr>
<td>
<code>statusCode</code><br/>
<em>
int32
</em>
</td>
<td>
<em>(Optional)</em>
<p>StatusCode is the HTTP status code to return for the aborted request</p>
</td>
</tr>
<tr>
<td>
<code>message</code><br/>
<em>
string
</em>
</td>
<td>
<em>(Optional)</em>
<p>Message is the HTTP status message to return for the aborted request</p>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.FaultInjectionConfig">FaultInjectionConfig
</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.FaultInjectionPolicySpec">FaultInjectionPolicySpec</a>, <a href="#gateway.flomesh.io/v1alpha1.GRPCFaultInjection">GRPCFaultInjection</a>, <a href="#gateway.flomesh.io/v1alpha1.HTTPFaultInjection">HTTPFaultInjection</a>, <a href="#gateway.flomesh.io/v1alpha1.HostnameFaultInjection">HostnameFaultInjection</a>)
</p>
<div>
<p>FaultInjectionConfig defines the access control configuration for a route</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>delay</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.FaultInjectionDelay">
FaultInjectionDelay
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Delay defines the delay configuration</p>
</td>
</tr>
<tr>
<td>
<code>abort</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.FaultInjectionAbort">
FaultInjectionAbort
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Abort defines the abort configuration</p>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.FaultInjectionDelay">FaultInjectionDelay
</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.FaultInjectionConfig">FaultInjectionConfig</a>)
</p>
<div>
<p>FaultInjectionDelay defines the delay configuration</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>percent</code><br/>
<em>
int32
</em>
</td>
<td>
<p>Percent is the percentage of requests to delay</p>
</td>
</tr>
<tr>
<td>
<code>fixed</code><br/>
<em>
int64
</em>
</td>
<td>
<em>(Optional)</em>
<p>Fixed is the fixed delay duration, default Unit is ms</p>
</td>
</tr>
<tr>
<td>
<code>range</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.FaultInjectionRange">
FaultInjectionRange
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Range is the range of delay duration</p>
</td>
</tr>
<tr>
<td>
<code>unit</code><br/>
<em>
string
</em>
</td>
<td>
<em>(Optional)</em>
<p>Unit is the unit of delay duration, default Unit is ms</p>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.FaultInjectionPolicySpec">FaultInjectionPolicySpec
</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.FaultInjectionPolicy">FaultInjectionPolicy</a>)
</p>
<div>
<p>FaultInjectionPolicySpec defines the desired state of FaultInjectionPolicy</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>targetRef</code><br/>
<em>
<a href="https://pkg.go.dev/sigs.k8s.io/gateway-api@v0.7.1/apis/v1alpha2#PolicyTargetReference">
sigs.k8s.io/gateway-api/apis/v1alpha2.PolicyTargetReference
</a>
</em>
</td>
<td>
<p>TargetRef is the reference to the target resource to which the policy is applied</p>
</td>
</tr>
<tr>
<td>
<code>hostnames</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.HostnameFaultInjection">
[]HostnameFaultInjection
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Hostnames is the access control configuration for hostnames</p>
</td>
</tr>
<tr>
<td>
<code>http</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.HTTPFaultInjection">
[]HTTPFaultInjection
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>HTTPFaultInjections is the access control configuration for HTTP routes</p>
</td>
</tr>
<tr>
<td>
<code>grpc</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.GRPCFaultInjection">
[]GRPCFaultInjection
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>GRPCFaultInjections is the access control configuration for GRPC routes</p>
</td>
</tr>
<tr>
<td>
<code>config</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.FaultInjectionConfig">
FaultInjectionConfig
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>DefaultConfig is the default access control for all ports, routes and hostnames</p>
</td>
</tr>
<tr>
<td>
<code>unit</code><br/>
<em>
string
</em>
</td>
<td>
<em>(Optional)</em>
<p>Unit is the unit of delay duration, default Unit is ms</p>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.FaultInjectionPolicyStatus">FaultInjectionPolicyStatus
</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.FaultInjectionPolicy">FaultInjectionPolicy</a>)
</p>
<div>
<p>FaultInjectionPolicyStatus defines the observed state of FaultInjectionPolicy</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>conditions</code><br/>
<em>
<a href="https://v1-26.docs.kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#condition-v1-meta">
[]Kubernetes meta/v1.Condition
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Conditions describe the current conditions of the FaultInjectionPolicy.</p>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.FaultInjectionRange">FaultInjectionRange
</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.FaultInjectionDelay">FaultInjectionDelay</a>)
</p>
<div>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>min</code><br/>
<em>
int64
</em>
</td>
<td>
<p>Min is the minimum value of the range, default Unit is ms</p>
</td>
</tr>
<tr>
<td>
<code>max</code><br/>
<em>
int64
</em>
</td>
<td>
<p>Max is the maximum value of the range, default Unit is ms</p>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.GRPCAccessControl">GRPCAccessControl
</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.AccessControlPolicySpec">AccessControlPolicySpec</a>)
</p>
<div>
<p>GRPCAccessControl defines the access control configuration for a GRPC route</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>match</code><br/>
<em>
<a href="https://pkg.go.dev/sigs.k8s.io/gateway-api@v0.7.1/apis/v1alpha2#GRPCRouteMatch">
sigs.k8s.io/gateway-api/apis/v1alpha2.GRPCRouteMatch
</a>
</em>
</td>
<td>
<p>Match is the match condition for the GRPC route</p>
</td>
</tr>
<tr>
<td>
<code>config</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.AccessControlConfig">
AccessControlConfig
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Config is the access control configuration for the GRPC route</p>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.GRPCFaultInjection">GRPCFaultInjection
</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.FaultInjectionPolicySpec">FaultInjectionPolicySpec</a>)
</p>
<div>
<p>GRPCFaultInjection defines the access control configuration for a GRPC route</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>match</code><br/>
<em>
<a href="https://pkg.go.dev/sigs.k8s.io/gateway-api@v0.7.1/apis/v1alpha2#GRPCRouteMatch">
sigs.k8s.io/gateway-api/apis/v1alpha2.GRPCRouteMatch
</a>
</em>
</td>
<td>
<p>Match is the match condition for the GRPC route</p>
</td>
</tr>
<tr>
<td>
<code>config</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.FaultInjectionConfig">
FaultInjectionConfig
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Config is the access control configuration for the GRPC route</p>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.GRPCRateLimit">GRPCRateLimit
</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.RateLimitPolicySpec">RateLimitPolicySpec</a>)
</p>
<div>
<p>GRPCRateLimit defines the rate limit configuration for a GRPC route</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>match</code><br/>
<em>
<a href="https://pkg.go.dev/sigs.k8s.io/gateway-api@v0.7.1/apis/v1alpha2#GRPCRouteMatch">
sigs.k8s.io/gateway-api/apis/v1alpha2.GRPCRouteMatch
</a>
</em>
</td>
<td>
<p>Match is the match condition for the GRPC route</p>
</td>
</tr>
<tr>
<td>
<code>config</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.L7RateLimit">
L7RateLimit
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Config is the rate limit configuration for the GRPC route</p>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.GatewayTLSConfig">GatewayTLSConfig
</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.GatewayTLSPolicySpec">GatewayTLSPolicySpec</a>, <a href="#gateway.flomesh.io/v1alpha1.PortGatewayTLS">PortGatewayTLS</a>)
</p>
<div>
<p>GatewayTLSConfig defines the Gateway TLS configuration</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>mTLS</code><br/>
<em>
bool
</em>
</td>
<td>
<em>(Optional)</em>
<p>MTLS defines if the gateway port should use mTLS or not</p>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.GatewayTLSPolicySpec">GatewayTLSPolicySpec
</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.GatewayTLSPolicy">GatewayTLSPolicy</a>)
</p>
<div>
<p>GatewayTLSPolicySpec defines the desired state of GatewayTLSPolicy</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>targetRef</code><br/>
<em>
<a href="https://pkg.go.dev/sigs.k8s.io/gateway-api@v0.7.1/apis/v1alpha2#PolicyTargetReference">
sigs.k8s.io/gateway-api/apis/v1alpha2.PolicyTargetReference
</a>
</em>
</td>
<td>
<p>TargetRef is the reference to the target resource to which the policy is applied</p>
</td>
</tr>
<tr>
<td>
<code>ports</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.PortGatewayTLS">
[]PortGatewayTLS
</a>
</em>
</td>
<td>
<p>Ports is the Gateway TLS configuration for ports</p>
</td>
</tr>
<tr>
<td>
<code>config</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.GatewayTLSConfig">
GatewayTLSConfig
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>DefaultConfig is the default Gateway TLS configuration for all ports</p>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.GatewayTLSPolicyStatus">GatewayTLSPolicyStatus
</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.GatewayTLSPolicy">GatewayTLSPolicy</a>)
</p>
<div>
<p>GatewayTLSPolicyStatus defines the observed state of GatewayTLSPolicy</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>conditions</code><br/>
<em>
<a href="https://v1-26.docs.kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#condition-v1-meta">
[]Kubernetes meta/v1.Condition
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Conditions describe the current conditions of the GatewayTLSPolicy.</p>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.HTTPAccessControl">HTTPAccessControl
</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.AccessControlPolicySpec">AccessControlPolicySpec</a>)
</p>
<div>
<p>HTTPAccessControl defines the access control configuration for a HTTP route</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>match</code><br/>
<em>
<a href="https://pkg.go.dev/sigs.k8s.io/gateway-api@v0.7.1/apis/v1beta1#HTTPRouteMatch">
sigs.k8s.io/gateway-api/apis/v1beta1.HTTPRouteMatch
</a>
</em>
</td>
<td>
<p>Match is the match condition for the HTTP route</p>
</td>
</tr>
<tr>
<td>
<code>config</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.AccessControlConfig">
AccessControlConfig
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Config is the access control configuration for the HTTP route</p>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.HTTPFaultInjection">HTTPFaultInjection
</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.FaultInjectionPolicySpec">FaultInjectionPolicySpec</a>)
</p>
<div>
<p>HTTPFaultInjection defines the access control configuration for a HTTP route</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>match</code><br/>
<em>
<a href="https://pkg.go.dev/sigs.k8s.io/gateway-api@v0.7.1/apis/v1beta1#HTTPRouteMatch">
sigs.k8s.io/gateway-api/apis/v1beta1.HTTPRouteMatch
</a>
</em>
</td>
<td>
<p>Match is the match condition for the HTTP route</p>
</td>
</tr>
<tr>
<td>
<code>config</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.FaultInjectionConfig">
FaultInjectionConfig
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Config is the access control configuration for the HTTP route</p>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.HTTPRateLimit">HTTPRateLimit
</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.RateLimitPolicySpec">RateLimitPolicySpec</a>)
</p>
<div>
<p>HTTPRateLimit defines the rate limit configuration for a HTTP route</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>match</code><br/>
<em>
<a href="https://pkg.go.dev/sigs.k8s.io/gateway-api@v0.7.1/apis/v1beta1#HTTPRouteMatch">
sigs.k8s.io/gateway-api/apis/v1beta1.HTTPRouteMatch
</a>
</em>
</td>
<td>
<p>Match is the match condition for the HTTP route</p>
</td>
</tr>
<tr>
<td>
<code>config</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.L7RateLimit">
L7RateLimit
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Config is the rate limit configuration for the HTTP route</p>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.HealthCheckConfig">HealthCheckConfig
</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.HealthCheckPolicySpec">HealthCheckPolicySpec</a>, <a href="#gateway.flomesh.io/v1alpha1.PortHealthCheck">PortHealthCheck</a>)
</p>
<div>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>interval</code><br/>
<em>
int32
</em>
</td>
<td>
<p>Interval is the interval in seconds to check the health of the service</p>
</td>
</tr>
<tr>
<td>
<code>maxFails</code><br/>
<em>
int32
</em>
</td>
<td>
<p>MaxFails is the maximum number of consecutive failed health checks before considering the service as unhealthy</p>
</td>
</tr>
<tr>
<td>
<code>failTimeout</code><br/>
<em>
int32
</em>
</td>
<td>
<em>(Optional)</em>
<p>FailTimeout is the time in seconds before considering the service as healthy if it&rsquo;s marked as unhealthy, even if it&rsquo;s already healthy</p>
</td>
</tr>
<tr>
<td>
<code>path</code><br/>
<em>
string
</em>
</td>
<td>
<em>(Optional)</em>
<p>Path is the path to check the health of the HTTP service, if it&rsquo;s not set, the health check will be TCP based</p>
</td>
</tr>
<tr>
<td>
<code>matches</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.HealthCheckMatch">
[]HealthCheckMatch
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Matches is the list of health check match conditions of HTTP service</p>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.HealthCheckMatch">HealthCheckMatch
</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.HealthCheckConfig">HealthCheckConfig</a>)
</p>
<div>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>statusCodes</code><br/>
<em>
[]int32
</em>
</td>
<td>
<em>(Optional)</em>
<p>StatusCodes is the list of status codes to match</p>
</td>
</tr>
<tr>
<td>
<code>body</code><br/>
<em>
string
</em>
</td>
<td>
<em>(Optional)</em>
<p>Body is the content of response body to match</p>
</td>
</tr>
<tr>
<td>
<code>headers</code><br/>
<em>
<a href="https://pkg.go.dev/sigs.k8s.io/gateway-api@v0.7.1/apis/v1beta1#HTTPHeader">
[]sigs.k8s.io/gateway-api/apis/v1beta1.HTTPHeader
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Headers is the list of response headers to match</p>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.HealthCheckPolicySpec">HealthCheckPolicySpec
</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.HealthCheckPolicy">HealthCheckPolicy</a>)
</p>
<div>
<p>HealthCheckPolicySpec defines the desired state of HealthCheckPolicy</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>targetRef</code><br/>
<em>
<a href="https://pkg.go.dev/sigs.k8s.io/gateway-api@v0.7.1/apis/v1alpha2#PolicyTargetReference">
sigs.k8s.io/gateway-api/apis/v1alpha2.PolicyTargetReference
</a>
</em>
</td>
<td>
<p>TargetRef is the reference to the target resource to which the policy is applied</p>
</td>
</tr>
<tr>
<td>
<code>ports</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.PortHealthCheck">
[]PortHealthCheck
</a>
</em>
</td>
<td>
<p>Ports is the health check configuration for ports</p>
</td>
</tr>
<tr>
<td>
<code>config</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.HealthCheckConfig">
HealthCheckConfig
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>DefaultConfig is the default health check configuration for all ports</p>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.HealthCheckPolicyStatus">HealthCheckPolicyStatus
</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.HealthCheckPolicy">HealthCheckPolicy</a>)
</p>
<div>
<p>HealthCheckPolicyStatus defines the observed state of HealthCheckPolicy</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>conditions</code><br/>
<em>
<a href="https://v1-26.docs.kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#condition-v1-meta">
[]Kubernetes meta/v1.Condition
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Conditions describe the current conditions of the HealthCheckPolicy.</p>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.HostnameAccessControl">HostnameAccessControl
</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.AccessControlPolicySpec">AccessControlPolicySpec</a>)
</p>
<div>
<p>HostnameAccessControl defines the access control configuration for a hostname</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>hostname</code><br/>
<em>
<a href="https://pkg.go.dev/sigs.k8s.io/gateway-api@v0.7.1/apis/v1beta1#Hostname">
sigs.k8s.io/gateway-api/apis/v1beta1.Hostname
</a>
</em>
</td>
<td>
<p>Hostname is the hostname for matching the access control</p>
</td>
</tr>
<tr>
<td>
<code>config</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.AccessControlConfig">
AccessControlConfig
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Config is the access control configuration for the hostname</p>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.HostnameFaultInjection">HostnameFaultInjection
</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.FaultInjectionPolicySpec">FaultInjectionPolicySpec</a>)
</p>
<div>
<p>HostnameFaultInjection defines the access control configuration for a hostname</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>hostname</code><br/>
<em>
<a href="https://pkg.go.dev/sigs.k8s.io/gateway-api@v0.7.1/apis/v1beta1#Hostname">
sigs.k8s.io/gateway-api/apis/v1beta1.Hostname
</a>
</em>
</td>
<td>
<p>Hostname is the hostname for matching the access control</p>
</td>
</tr>
<tr>
<td>
<code>config</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.FaultInjectionConfig">
FaultInjectionConfig
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Config is the access control configuration for the hostname</p>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.HostnameRateLimit">HostnameRateLimit
</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.RateLimitPolicySpec">RateLimitPolicySpec</a>)
</p>
<div>
<p>HostnameRateLimit defines the rate limit configuration for a hostname</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>hostname</code><br/>
<em>
<a href="https://pkg.go.dev/sigs.k8s.io/gateway-api@v0.7.1/apis/v1beta1#Hostname">
sigs.k8s.io/gateway-api/apis/v1beta1.Hostname
</a>
</em>
</td>
<td>
<p>Hostname is the hostname for matching the rate limit</p>
</td>
</tr>
<tr>
<td>
<code>config</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.L7RateLimit">
L7RateLimit
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Config is the rate limit configuration for the hostname</p>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.L7RateLimit">L7RateLimit
</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.GRPCRateLimit">GRPCRateLimit</a>, <a href="#gateway.flomesh.io/v1alpha1.HTTPRateLimit">HTTPRateLimit</a>, <a href="#gateway.flomesh.io/v1alpha1.HostnameRateLimit">HostnameRateLimit</a>, <a href="#gateway.flomesh.io/v1alpha1.RateLimitPolicySpec">RateLimitPolicySpec</a>)
</p>
<div>
<p>L7RateLimit defines the rate limit configuration for a route</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>mode</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.RateLimitPolicyMode">
RateLimitPolicyMode
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Mode is the mode of the rate limit policy, Local or Global, default is Local</p>
</td>
</tr>
<tr>
<td>
<code>backlog</code><br/>
<em>
int32
</em>
</td>
<td>
<em>(Optional)</em>
<p>Backlog is the number of requests allowed to wait in the queue</p>
</td>
</tr>
<tr>
<td>
<code>requests</code><br/>
<em>
int32
</em>
</td>
<td>
<p>Requests is the number of requests allowed per statTimeWindow</p>
</td>
</tr>
<tr>
<td>
<code>burst</code><br/>
<em>
int32
</em>
</td>
<td>
<em>(Optional)</em>
<p>Burst is the number of requests allowed to be bursted, if not specified, it will be the same as Requests</p>
</td>
</tr>
<tr>
<td>
<code>statTimeWindow</code><br/>
<em>
int32
</em>
</td>
<td>
<p>StatTimeWindow is the time window in seconds</p>
</td>
</tr>
<tr>
<td>
<code>responseStatusCode</code><br/>
<em>
int32
</em>
</td>
<td>
<em>(Optional)</em>
<p>ResponseStatusCode is the response status code to be returned when the rate limit is exceeded</p>
</td>
</tr>
<tr>
<td>
<code>responseHeadersToAdd</code><br/>
<em>
<a href="https://pkg.go.dev/sigs.k8s.io/gateway-api@v0.7.1/apis/v1beta1#HTTPHeader">
[]sigs.k8s.io/gateway-api/apis/v1beta1.HTTPHeader
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>ResponseHeadersToAdd is the response headers to be added when the rate limit is exceeded</p>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.LoadBalancerPolicySpec">LoadBalancerPolicySpec
</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.LoadBalancerPolicy">LoadBalancerPolicy</a>)
</p>
<div>
<p>LoadBalancerPolicySpec defines the desired state of LoadBalancerPolicy</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>targetRef</code><br/>
<em>
<a href="https://pkg.go.dev/sigs.k8s.io/gateway-api@v0.7.1/apis/v1alpha2#PolicyTargetReference">
sigs.k8s.io/gateway-api/apis/v1alpha2.PolicyTargetReference
</a>
</em>
</td>
<td>
<p>TargetRef is the reference to the target resource to which the policy is applied</p>
</td>
</tr>
<tr>
<td>
<code>ports</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.PortLoadBalancer">
[]PortLoadBalancer
</a>
</em>
</td>
<td>
<p>Ports is the load balancer configuration for ports</p>
</td>
</tr>
<tr>
<td>
<code>type</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.LoadBalancerType">
LoadBalancerType
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>DefaultType is the default type of the load balancer for all ports</p>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.LoadBalancerPolicyStatus">LoadBalancerPolicyStatus
</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.LoadBalancerPolicy">LoadBalancerPolicy</a>)
</p>
<div>
<p>LoadBalancerPolicyStatus defines the observed state of LoadBalancerPolicy</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>conditions</code><br/>
<em>
<a href="https://v1-26.docs.kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#condition-v1-meta">
[]Kubernetes meta/v1.Condition
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Conditions describe the current conditions of the LoadBalancerPolicy.</p>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.LoadBalancerType">LoadBalancerType
(<code>string</code> alias)</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.LoadBalancerPolicySpec">LoadBalancerPolicySpec</a>, <a href="#gateway.flomesh.io/v1alpha1.PortLoadBalancer">PortLoadBalancer</a>)
</p>
<div>
</div>
<table>
<thead>
<tr>
<th>Value</th>
<th>Description</th>
</tr>
</thead>
<tbody><tr><td><p>&#34;HashingLoadBalancer&#34;</p></td>
<td></td>
</tr><tr><td><p>&#34;LeastConnectionLoadBalancer&#34;</p></td>
<td></td>
</tr><tr><td><p>&#34;RoundRobinLoadBalancer&#34;</p></td>
<td></td>
</tr></tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.PortAccessControl">PortAccessControl
</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.AccessControlPolicySpec">AccessControlPolicySpec</a>)
</p>
<div>
<p>PortAccessControl defines the access control configuration for a port</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>port</code><br/>
<em>
<a href="https://pkg.go.dev/sigs.k8s.io/gateway-api@v0.7.1/apis/v1beta1#PortNumber">
sigs.k8s.io/gateway-api/apis/v1beta1.PortNumber
</a>
</em>
</td>
<td>
<p>Port is the port number for matching the access control</p>
</td>
</tr>
<tr>
<td>
<code>config</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.AccessControlConfig">
AccessControlConfig
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Config is the access control configuration for the port</p>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.PortCircuitBreaking">PortCircuitBreaking
</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.CircuitBreakingPolicySpec">CircuitBreakingPolicySpec</a>)
</p>
<div>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>port</code><br/>
<em>
<a href="https://pkg.go.dev/sigs.k8s.io/gateway-api@v0.7.1/apis/v1beta1#PortNumber">
sigs.k8s.io/gateway-api/apis/v1beta1.PortNumber
</a>
</em>
</td>
<td>
<p>Port is the port number of the target service</p>
</td>
</tr>
<tr>
<td>
<code>config</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.CircuitBreakingConfig">
CircuitBreakingConfig
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Config is the circuit breaking configuration for the port</p>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.PortGatewayTLS">PortGatewayTLS
</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.GatewayTLSPolicySpec">GatewayTLSPolicySpec</a>)
</p>
<div>
<p>PortGatewayTLS defines the Gateway TLS configuration for a port</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>port</code><br/>
<em>
<a href="https://pkg.go.dev/sigs.k8s.io/gateway-api@v0.7.1/apis/v1beta1#PortNumber">
sigs.k8s.io/gateway-api/apis/v1beta1.PortNumber
</a>
</em>
</td>
<td>
<p>Port is the port number of the target service</p>
</td>
</tr>
<tr>
<td>
<code>config</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.GatewayTLSConfig">
GatewayTLSConfig
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Config is the Gateway TLS configuration for the port</p>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.PortHealthCheck">PortHealthCheck
</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.HealthCheckPolicySpec">HealthCheckPolicySpec</a>)
</p>
<div>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>port</code><br/>
<em>
<a href="https://pkg.go.dev/sigs.k8s.io/gateway-api@v0.7.1/apis/v1beta1#PortNumber">
sigs.k8s.io/gateway-api/apis/v1beta1.PortNumber
</a>
</em>
</td>
<td>
<p>Port is the port number of the target service</p>
</td>
</tr>
<tr>
<td>
<code>config</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.HealthCheckConfig">
HealthCheckConfig
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Config is the health check configuration for the port</p>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.PortLoadBalancer">PortLoadBalancer
</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.LoadBalancerPolicySpec">LoadBalancerPolicySpec</a>)
</p>
<div>
<p>PortLoadBalancer defines the load balancer configuration for a port</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>port</code><br/>
<em>
<a href="https://pkg.go.dev/sigs.k8s.io/gateway-api@v0.7.1/apis/v1beta1#PortNumber">
sigs.k8s.io/gateway-api/apis/v1beta1.PortNumber
</a>
</em>
</td>
<td>
<p>Port is the port number for matching the load balancer</p>
</td>
</tr>
<tr>
<td>
<code>type</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.LoadBalancerType">
LoadBalancerType
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Type is the type of the load balancer</p>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.PortRateLimit">PortRateLimit
</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.RateLimitPolicySpec">RateLimitPolicySpec</a>)
</p>
<div>
<p>PortRateLimit defines the rate limit configuration for a port</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>port</code><br/>
<em>
<a href="https://pkg.go.dev/sigs.k8s.io/gateway-api@v0.7.1/apis/v1beta1#PortNumber">
sigs.k8s.io/gateway-api/apis/v1beta1.PortNumber
</a>
</em>
</td>
<td>
<p>Port is the port number for matching the rate limit</p>
</td>
</tr>
<tr>
<td>
<code>bps</code><br/>
<em>
int64
</em>
</td>
<td>
<em>(Optional)</em>
<p>BPS is the rate limit in bytes per second for the port</p>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.PortRetry">PortRetry
</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.RetryPolicySpec">RetryPolicySpec</a>)
</p>
<div>
<p>PortRetry defines the retry configuration for a port</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>port</code><br/>
<em>
<a href="https://pkg.go.dev/sigs.k8s.io/gateway-api@v0.7.1/apis/v1beta1#PortNumber">
sigs.k8s.io/gateway-api/apis/v1beta1.PortNumber
</a>
</em>
</td>
<td>
<p>Port is the port number of the target service</p>
</td>
</tr>
<tr>
<td>
<code>config</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.RetryConfig">
RetryConfig
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Config is the retry configuration for the port</p>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.PortSessionSticky">PortSessionSticky
</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.SessionStickyPolicySpec">SessionStickyPolicySpec</a>)
</p>
<div>
<p>PortSessionSticky defines the session sticky configuration for a port</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>port</code><br/>
<em>
<a href="https://pkg.go.dev/sigs.k8s.io/gateway-api@v0.7.1/apis/v1beta1#PortNumber">
sigs.k8s.io/gateway-api/apis/v1beta1.PortNumber
</a>
</em>
</td>
<td>
<p>Port is the port number of the target service</p>
</td>
</tr>
<tr>
<td>
<code>config</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.SessionStickyConfig">
SessionStickyConfig
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Config is the session sticky configuration for the port</p>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.PortUpstreamTLS">PortUpstreamTLS
</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.UpstreamTLSPolicySpec">UpstreamTLSPolicySpec</a>)
</p>
<div>
<p>PortUpstreamTLS defines the session sticky configuration for a port</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>port</code><br/>
<em>
<a href="https://pkg.go.dev/sigs.k8s.io/gateway-api@v0.7.1/apis/v1beta1#PortNumber">
sigs.k8s.io/gateway-api/apis/v1beta1.PortNumber
</a>
</em>
</td>
<td>
<p>Port is the port number of the target service</p>
</td>
</tr>
<tr>
<td>
<code>config</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.UpstreamTLSConfig">
UpstreamTLSConfig
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Config is the session sticky configuration for the port</p>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.RateLimitPolicyMode">RateLimitPolicyMode
(<code>string</code> alias)</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.L7RateLimit">L7RateLimit</a>)
</p>
<div>
</div>
<table>
<thead>
<tr>
<th>Value</th>
<th>Description</th>
</tr>
</thead>
<tbody><tr><td><p>&#34;Global&#34;</p></td>
<td><p>RateLimitPolicyModeGlobal is the global mode</p>
</td>
</tr><tr><td><p>&#34;Local&#34;</p></td>
<td><p>RateLimitPolicyModeLocal is the local mode</p>
</td>
</tr></tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.RateLimitPolicySpec">RateLimitPolicySpec
</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.RateLimitPolicy">RateLimitPolicy</a>)
</p>
<div>
<p>RateLimitPolicySpec defines the desired state of RateLimitPolicy</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>targetRef</code><br/>
<em>
<a href="https://pkg.go.dev/sigs.k8s.io/gateway-api@v0.7.1/apis/v1alpha2#PolicyTargetReference">
sigs.k8s.io/gateway-api/apis/v1alpha2.PolicyTargetReference
</a>
</em>
</td>
<td>
<p>TargetRef is the reference to the target resource to which the policy is applied</p>
</td>
</tr>
<tr>
<td>
<code>ports</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.PortRateLimit">
[]PortRateLimit
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Ports is the rate limit configuration for ports</p>
</td>
</tr>
<tr>
<td>
<code>bps</code><br/>
<em>
int64
</em>
</td>
<td>
<em>(Optional)</em>
<p>DefaultBPS is the default rate limit for all ports</p>
</td>
</tr>
<tr>
<td>
<code>hostnames</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.HostnameRateLimit">
[]HostnameRateLimit
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Hostnames is the rate limit configuration for hostnames</p>
</td>
</tr>
<tr>
<td>
<code>http</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.HTTPRateLimit">
[]HTTPRateLimit
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>HTTPRateLimits is the rate limit configuration for HTTP routes</p>
</td>
</tr>
<tr>
<td>
<code>grpc</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.GRPCRateLimit">
[]GRPCRateLimit
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>GRPCRateLimits is the rate limit configuration for GRPC routes</p>
</td>
</tr>
<tr>
<td>
<code>config</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.L7RateLimit">
L7RateLimit
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>DefaultConfig is the default rate limit for all routes and hostnames</p>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.RateLimitPolicyStatus">RateLimitPolicyStatus
</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.RateLimitPolicy">RateLimitPolicy</a>)
</p>
<div>
<p>RateLimitPolicyStatus defines the observed state of RateLimitPolicy</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>conditions</code><br/>
<em>
<a href="https://v1-26.docs.kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#condition-v1-meta">
[]Kubernetes meta/v1.Condition
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Conditions describe the current conditions of the RateLimitPolicy.</p>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.RetryConfig">RetryConfig
</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.PortRetry">PortRetry</a>, <a href="#gateway.flomesh.io/v1alpha1.RetryPolicySpec">RetryPolicySpec</a>)
</p>
<div>
<p>RetryConfig defines the retry configuration</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>retryOn</code><br/>
<em>
[]string
</em>
</td>
<td>
<p>RetryOn is the list of retryable response codes, e.g. 5xx matches 500-599, or 500 matches just 500</p>
</td>
</tr>
<tr>
<td>
<code>numRetries</code><br/>
<em>
int32
</em>
</td>
<td>
<em>(Optional)</em>
<p>NumRetries is the number of retries</p>
</td>
</tr>
<tr>
<td>
<code>backoffBaseInterval</code><br/>
<em>
float32
</em>
</td>
<td>
<em>(Optional)</em>
<p>BackoffBaseInterval is the base interval for computing backoff in seconds</p>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.RetryPolicySpec">RetryPolicySpec
</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.RetryPolicy">RetryPolicy</a>)
</p>
<div>
<p>RetryPolicySpec defines the desired state of RetryPolicy</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>targetRef</code><br/>
<em>
<a href="https://pkg.go.dev/sigs.k8s.io/gateway-api@v0.7.1/apis/v1alpha2#PolicyTargetReference">
sigs.k8s.io/gateway-api/apis/v1alpha2.PolicyTargetReference
</a>
</em>
</td>
<td>
<p>TargetRef is the reference to the target resource to which the policy is applied</p>
</td>
</tr>
<tr>
<td>
<code>ports</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.PortRetry">
[]PortRetry
</a>
</em>
</td>
<td>
<p>Ports is the retry configuration for ports</p>
</td>
</tr>
<tr>
<td>
<code>config</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.RetryConfig">
RetryConfig
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>DefaultConfig is the default retry configuration for all ports</p>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.RetryPolicyStatus">RetryPolicyStatus
</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.RetryPolicy">RetryPolicy</a>)
</p>
<div>
<p>RetryPolicyStatus defines the observed state of RetryPolicy</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>conditions</code><br/>
<em>
<a href="https://v1-26.docs.kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#condition-v1-meta">
[]Kubernetes meta/v1.Condition
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Conditions describe the current conditions of the RetryPolicy.</p>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.SessionStickyConfig">SessionStickyConfig
</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.PortSessionSticky">PortSessionSticky</a>, <a href="#gateway.flomesh.io/v1alpha1.SessionStickyPolicySpec">SessionStickyPolicySpec</a>)
</p>
<div>
<p>SessionStickyConfig defines the session sticky configuration</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>cookieName</code><br/>
<em>
string
</em>
</td>
<td>
<em>(Optional)</em>
<p>CookieName is the name of the cookie used for sticky session</p>
</td>
</tr>
<tr>
<td>
<code>expires</code><br/>
<em>
int32
</em>
</td>
<td>
<em>(Optional)</em>
<p>Expires is the expiration time of the cookie in seconds</p>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.SessionStickyPolicySpec">SessionStickyPolicySpec
</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.SessionStickyPolicy">SessionStickyPolicy</a>)
</p>
<div>
<p>SessionStickyPolicySpec defines the desired state of SessionStickyPolicy</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>targetRef</code><br/>
<em>
<a href="https://pkg.go.dev/sigs.k8s.io/gateway-api@v0.7.1/apis/v1alpha2#PolicyTargetReference">
sigs.k8s.io/gateway-api/apis/v1alpha2.PolicyTargetReference
</a>
</em>
</td>
<td>
<p>TargetRef is the reference to the target resource to which the policy is applied</p>
</td>
</tr>
<tr>
<td>
<code>ports</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.PortSessionSticky">
[]PortSessionSticky
</a>
</em>
</td>
<td>
<p>Ports is the session sticky configuration for ports</p>
</td>
</tr>
<tr>
<td>
<code>config</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.SessionStickyConfig">
SessionStickyConfig
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>DefaultConfig is the default session sticky configuration for all ports</p>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.SessionStickyPolicyStatus">SessionStickyPolicyStatus
</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.SessionStickyPolicy">SessionStickyPolicy</a>)
</p>
<div>
<p>SessionStickyPolicyStatus defines the observed state of SessionStickyPolicy</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>conditions</code><br/>
<em>
<a href="https://v1-26.docs.kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#condition-v1-meta">
[]Kubernetes meta/v1.Condition
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Conditions describe the current conditions of the SessionStickyPolicy.</p>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.UpstreamTLSConfig">UpstreamTLSConfig
</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.PortUpstreamTLS">PortUpstreamTLS</a>, <a href="#gateway.flomesh.io/v1alpha1.UpstreamTLSPolicySpec">UpstreamTLSPolicySpec</a>)
</p>
<div>
<p>UpstreamTLSConfig defines the session sticky configuration</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>certificateRef</code><br/>
<em>
<a href="https://pkg.go.dev/sigs.k8s.io/gateway-api@v0.7.1/apis/v1beta1#SecretObjectReference">
sigs.k8s.io/gateway-api/apis/v1beta1.SecretObjectReference
</a>
</em>
</td>
<td>
<p>CertificateRef is the reference to the certificate used for TLS connection to upstream</p>
</td>
</tr>
<tr>
<td>
<code>mTLS</code><br/>
<em>
bool
</em>
</td>
<td>
<em>(Optional)</em>
<p>MTLS is the flag to enable mutual TLS to upstream</p>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.UpstreamTLSPolicySpec">UpstreamTLSPolicySpec
</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.UpstreamTLSPolicy">UpstreamTLSPolicy</a>)
</p>
<div>
<p>UpstreamTLSPolicySpec defines the desired state of UpstreamTLSPolicy</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>targetRef</code><br/>
<em>
<a href="https://pkg.go.dev/sigs.k8s.io/gateway-api@v0.7.1/apis/v1alpha2#PolicyTargetReference">
sigs.k8s.io/gateway-api/apis/v1alpha2.PolicyTargetReference
</a>
</em>
</td>
<td>
<p>TargetRef is the reference to the target resource to which the policy is applied</p>
</td>
</tr>
<tr>
<td>
<code>ports</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.PortUpstreamTLS">
[]PortUpstreamTLS
</a>
</em>
</td>
<td>
<p>Ports is the session sticky configuration for ports</p>
</td>
</tr>
<tr>
<td>
<code>config</code><br/>
<em>
<a href="#gateway.flomesh.io/v1alpha1.UpstreamTLSConfig">
UpstreamTLSConfig
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>DefaultConfig is the default session sticky configuration for all ports</p>
</td>
</tr>
</tbody>
</table>
<h3 id="gateway.flomesh.io/v1alpha1.UpstreamTLSPolicyStatus">UpstreamTLSPolicyStatus
</h3>
<p>
(<em>Appears on:</em><a href="#gateway.flomesh.io/v1alpha1.UpstreamTLSPolicy">UpstreamTLSPolicy</a>)
</p>
<div>
<p>UpstreamTLSPolicyStatus defines the observed state of UpstreamTLSPolicy</p>
</div>
<table>
<thead>
<tr>
<th>Field</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr>
<td>
<code>conditions</code><br/>
<em>
<a href="https://v1-26.docs.kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#condition-v1-meta">
[]Kubernetes meta/v1.Condition
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Conditions describe the current conditions of the UpstreamTLSPolicy.</p>
</td>
</tr>
</tbody>
</table>
<hr/>
<p><em>
Generated with <code>gen-crd-api-reference-docs</code>
on git commit <code>8abe9ab</code>.
</em></p>
