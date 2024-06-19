---
title: "Config v1alpha3 API Reference"
description: "Config v1alpha3 API reference documentation."
type: docs
---

<p>Packages:</p>
<ul>
<li>
<a href="#config.flomesh.io%2fv1alpha3">config.flomesh.io/v1alpha3</a>
</li>
</ul>
<h2 id="config.flomesh.io/v1alpha3">config.flomesh.io/v1alpha3</h2>
<div>
<p>Package v1alpha3 is the v1alpha3 version of the API.</p>
</div>
Resource Types:
<ul></ul>
<h3 id="config.flomesh.io/v1alpha3.CertManagerProviderSpec">CertManagerProviderSpec
</h3>
<p>
(<em>Appears on:</em><a href="#config.flomesh.io/v1alpha3.ProviderSpec">ProviderSpec</a>)
</p>
<div>
<p>CertManagerProviderSpec defines the configuration of the cert-manager provider</p>
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
<code>issuerName</code><br/>
<em>
string
</em>
</td>
<td>
<p>IssuerName specifies the name of the Issuer resource</p>
</td>
</tr>
<tr>
<td>
<code>issuerKind</code><br/>
<em>
string
</em>
</td>
<td>
<p>IssuerKind specifies the kind of Issuer</p>
</td>
</tr>
<tr>
<td>
<code>issuerGroup</code><br/>
<em>
string
</em>
</td>
<td>
<p>IssuerGroup specifies the group the Issuer belongs to</p>
</td>
</tr>
</tbody>
</table>
<h3 id="config.flomesh.io/v1alpha3.CertificateSpec">CertificateSpec
</h3>
<p>
(<em>Appears on:</em><a href="#config.flomesh.io/v1alpha3.MeshConfigSpec">MeshConfigSpec</a>)
</p>
<div>
<p>CertificateSpec is the type to reperesent FSM&rsquo;s certificate management configuration.</p>
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
<code>serviceCertValidityDuration</code><br/>
<em>
string
</em>
</td>
<td>
<p>ServiceCertValidityDuration defines the service certificate validity duration.</p>
</td>
</tr>
<tr>
<td>
<code>certKeyBitSize</code><br/>
<em>
int
</em>
</td>
<td>
<p>CertKeyBitSize defines the certicate key bit size.</p>
</td>
</tr>
<tr>
<td>
<code>ingressGateway</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.IngressGatewayCertSpec">
IngressGatewayCertSpec
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>IngressGateway defines the certificate specification for an ingress gateway.</p>
</td>
</tr>
</tbody>
</table>
<h3 id="config.flomesh.io/v1alpha3.ClusterPropertySpec">ClusterPropertySpec
</h3>
<p>
(<em>Appears on:</em><a href="#config.flomesh.io/v1alpha3.ClusterSetSpec">ClusterSetSpec</a>)
</p>
<div>
<p>ClusterPropertySpec is the type to represent cluster property.</p>
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
<code>name</code><br/>
<em>
string
</em>
</td>
<td>
<p>Name defines the name of cluster property.</p>
</td>
</tr>
<tr>
<td>
<code>value</code><br/>
<em>
string
</em>
</td>
<td>
<p>Value defines the name of cluster property.</p>
</td>
</tr>
</tbody>
</table>
<h3 id="config.flomesh.io/v1alpha3.ClusterSetSpec">ClusterSetSpec
</h3>
<p>
(<em>Appears on:</em><a href="#config.flomesh.io/v1alpha3.MeshConfigSpec">MeshConfigSpec</a>)
</p>
<div>
<p>ClusterSetSpec is the type to represent cluster set.</p>
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
<code>isManaged</code><br/>
<em>
bool
</em>
</td>
<td>
<p>IsManaged defines if the cluster is managed.</p>
</td>
</tr>
<tr>
<td>
<code>uid</code><br/>
<em>
string
</em>
</td>
<td>
<p>UID defines Unique ID of cluster.</p>
</td>
</tr>
<tr>
<td>
<code>region</code><br/>
<em>
string
</em>
</td>
<td>
<em>(Optional)</em>
<p>Region defines Region of cluster.</p>
</td>
</tr>
<tr>
<td>
<code>zone</code><br/>
<em>
string
</em>
</td>
<td>
<em>(Optional)</em>
<p>Zone defines Zone of cluster.</p>
</td>
</tr>
<tr>
<td>
<code>group</code><br/>
<em>
string
</em>
</td>
<td>
<em>(Optional)</em>
<p>Group defines Group of cluster.</p>
</td>
</tr>
<tr>
<td>
<code>name</code><br/>
<em>
string
</em>
</td>
<td>
<p>Name defines Name of cluster.</p>
</td>
</tr>
<tr>
<td>
<code>controlPlaneUID</code><br/>
<em>
string
</em>
</td>
<td>
<p>ControlPlaneUID defines the unique ID of the control plane cluster,
in case it&rsquo;s managed</p>
</td>
</tr>
<tr>
<td>
<code>properties</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.ClusterPropertySpec">
[]ClusterPropertySpec
</a>
</em>
</td>
<td>
<p>Properties defines properties for cluster.</p>
</td>
</tr>
</tbody>
</table>
<h3 id="config.flomesh.io/v1alpha3.EgressGatewaySpec">EgressGatewaySpec
</h3>
<p>
(<em>Appears on:</em><a href="#config.flomesh.io/v1alpha3.MeshConfigSpec">MeshConfigSpec</a>)
</p>
<div>
<p>EgressGatewaySpec is the type to represent egress gateway.</p>
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
<code>enabled</code><br/>
<em>
bool
</em>
</td>
<td>
<p>Enabled defines if flb is enabled.</p>
</td>
</tr>
<tr>
<td>
<code>logLevel</code><br/>
<em>
string
</em>
</td>
<td>
<p>LogLevel defines the log level of gateway api.</p>
</td>
</tr>
<tr>
<td>
<code>mode</code><br/>
<em>
string
</em>
</td>
<td>
<p>Mode defines the mode of egress gateway.</p>
</td>
</tr>
<tr>
<td>
<code>port</code><br/>
<em>
int32
</em>
</td>
<td>
<p>Port defines the port of egress gateway.</p>
</td>
</tr>
<tr>
<td>
<code>adminPort</code><br/>
<em>
int32
</em>
</td>
<td>
<p>AdminPort defines the admin port of egress gateway.</p>
</td>
</tr>
<tr>
<td>
<code>replicas</code><br/>
<em>
int32
</em>
</td>
<td>
<p>Replicas defines the replicas of egress gateway.</p>
</td>
</tr>
</tbody>
</table>
<h3 id="config.flomesh.io/v1alpha3.ExternalAuthzSpec">ExternalAuthzSpec
</h3>
<p>
(<em>Appears on:</em><a href="#config.flomesh.io/v1alpha3.TrafficSpec">TrafficSpec</a>)
</p>
<div>
<p>ExternalAuthzSpec is a type to represent external authorization configuration.</p>
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
<code>enable</code><br/>
<em>
bool
</em>
</td>
<td>
<p>Enable defines a boolean indicating if the external authorization policy is to be enabled.</p>
</td>
</tr>
<tr>
<td>
<code>address</code><br/>
<em>
string
</em>
</td>
<td>
<p>Address defines the remote address of the external authorization endpoint.</p>
</td>
</tr>
<tr>
<td>
<code>port</code><br/>
<em>
uint16
</em>
</td>
<td>
<p>Port defines the destination port of the remote external authorization endpoint.</p>
</td>
</tr>
<tr>
<td>
<code>statPrefix</code><br/>
<em>
string
</em>
</td>
<td>
<p>StatPrefix defines a prefix for the stats sink for this external authorization policy.</p>
</td>
</tr>
<tr>
<td>
<code>timeout</code><br/>
<em>
string
</em>
</td>
<td>
<p>Timeout defines the timeout in which a response from the external authorization endpoint.
is expected to execute.</p>
</td>
</tr>
<tr>
<td>
<code>failureModeAllow</code><br/>
<em>
bool
</em>
</td>
<td>
<p>FailureModeAllow defines a boolean indicating if traffic should be allowed on a failure to get a
response against the external authorization endpoint.</p>
</td>
</tr>
</tbody>
</table>
<h3 id="config.flomesh.io/v1alpha3.FLBSpec">FLBSpec
</h3>
<p>
(<em>Appears on:</em><a href="#config.flomesh.io/v1alpha3.MeshConfigSpec">MeshConfigSpec</a>)
</p>
<div>
<p>FLBSpec is the type to represent flb.</p>
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
<code>enabled</code><br/>
<em>
bool
</em>
</td>
<td>
<p>Enabled defines if flb is enabled.</p>
</td>
</tr>
<tr>
<td>
<code>strictMode</code><br/>
<em>
bool
</em>
</td>
<td>
<p>StrictMode defines if flb is in strict mode.</p>
</td>
</tr>
<tr>
<td>
<code>upstreamMode</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.FLBUpstreamMode">
FLBUpstreamMode
</a>
</em>
</td>
<td>
<p>UpstreamMode defines the upstream mode of flb.</p>
</td>
</tr>
<tr>
<td>
<code>secretName</code><br/>
<em>
string
</em>
</td>
<td>
<p>SecretName defines the secret name of flb.</p>
</td>
</tr>
</tbody>
</table>
<h3 id="config.flomesh.io/v1alpha3.FLBUpstreamMode">FLBUpstreamMode
(<code>string</code> alias)</h3>
<p>
(<em>Appears on:</em><a href="#config.flomesh.io/v1alpha3.FLBSpec">FLBSpec</a>)
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
<tbody><tr><td><p>&#34;Endpoint&#34;</p></td>
<td></td>
</tr><tr><td><p>&#34;NodePort&#34;</p></td>
<td></td>
</tr></tbody>
</table>
<h3 id="config.flomesh.io/v1alpha3.FeatureFlags">FeatureFlags
</h3>
<p>
(<em>Appears on:</em><a href="#config.flomesh.io/v1alpha3.MeshConfigSpec">MeshConfigSpec</a>)
</p>
<div>
<p>FeatureFlags is a type to represent FSM&rsquo;s feature flags.</p>
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
<code>enableEgressPolicy</code><br/>
<em>
bool
</em>
</td>
<td>
<p>EnableEgressPolicy defines if FSM&rsquo;s Egress policy is enabled.</p>
</td>
</tr>
<tr>
<td>
<code>enableSnapshotCacheMode</code><br/>
<em>
bool
</em>
</td>
<td>
<p>EnableSnapshotCacheMode defines if XDS server starts with snapshot cache.</p>
</td>
</tr>
<tr>
<td>
<code>enableAsyncProxyServiceMapping</code><br/>
<em>
bool
</em>
</td>
<td>
<p>EnableAsyncProxyServiceMapping defines if FSM will map proxies to services asynchronously.</p>
</td>
</tr>
<tr>
<td>
<code>enableIngressBackendPolicy</code><br/>
<em>
bool
</em>
</td>
<td>
<p>EnableIngressBackendPolicy defines if FSM will use the IngressBackend API to allow ingress traffic to
service mesh backends.</p>
</td>
</tr>
<tr>
<td>
<code>enableAccessControlPolicy</code><br/>
<em>
bool
</em>
</td>
<td>
<p>EnableAccessControlPolicy defines if FSM will use the AccessControl API to allow access control traffic to
service mesh backends.</p>
</td>
</tr>
<tr>
<td>
<code>enableAccessCertPolicy</code><br/>
<em>
bool
</em>
</td>
<td>
<p>EnableAccessCertPolicy defines if FSM can issue certificates for external services..</p>
</td>
</tr>
<tr>
<td>
<code>enableSidecarActiveHealthChecks</code><br/>
<em>
bool
</em>
</td>
<td>
<p>EnableSidecarActiveHealthChecks defines if FSM will Sidecar active health
checks between services allowed to communicate.</p>
</td>
</tr>
<tr>
<td>
<code>enableRetryPolicy</code><br/>
<em>
bool
</em>
</td>
<td>
<p>EnableRetryPolicy defines if retry policy is enabled.</p>
</td>
</tr>
<tr>
<td>
<code>enablePluginPolicy</code><br/>
<em>
bool
</em>
</td>
<td>
<p>EnablePluginPolicy defines if plugin policy is enabled.</p>
</td>
</tr>
<tr>
<td>
<code>enableAutoDefaultRoute</code><br/>
<em>
bool
</em>
</td>
<td>
<p>EnableAutoDefaultRoute defines if auto default route is enabled.</p>
</td>
</tr>
<tr>
<td>
<code>enableValidateGatewayListenerHostname</code><br/>
<em>
bool
</em>
</td>
<td>
<p>EnableValidateGatewayListenerHostname defines if validate gateway listener hostname is enabled.</p>
</td>
</tr>
<tr>
<td>
<code>enableValidateHTTPRouteHostnames</code><br/>
<em>
bool
</em>
</td>
<td>
<p>EnableValidateHTTPRouteHostnames defines if validate http route hostnames is enabled.</p>
</td>
</tr>
<tr>
<td>
<code>enableValidateGRPCRouteHostnames</code><br/>
<em>
bool
</em>
</td>
<td>
<p>EnableValidateGRPCRouteHostnames defines if validate grpc route hostnames is enabled.</p>
</td>
</tr>
<tr>
<td>
<code>enableValidateTLSRouteHostnames</code><br/>
<em>
bool
</em>
</td>
<td>
<p>EnableValidateTCPRouteHostnames defines if validate tcp route hostnames is enabled.</p>
</td>
</tr>
<tr>
<td>
<code>enableGatewayAgentService</code><br/>
<em>
bool
</em>
</td>
<td>
<p>EnableGatewayAgentService defines if agent service is enabled.</p>
</td>
</tr>
<tr>
<td>
<code>enableGatewayProxyTag</code><br/>
<em>
bool
</em>
</td>
<td>
<p>EnableGatewayProxyTag defines if gateway proxy-tag header is enabled.</p>
</td>
</tr>
</tbody>
</table>
<h3 id="config.flomesh.io/v1alpha3.GatewayAPISpec">GatewayAPISpec
</h3>
<p>
(<em>Appears on:</em><a href="#config.flomesh.io/v1alpha3.MeshConfigSpec">MeshConfigSpec</a>)
</p>
<div>
<p>GatewayAPISpec is the type to represent gateway api.</p>
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
<code>enabled</code><br/>
<em>
bool
</em>
</td>
<td>
<p>Enabled defines if gateway api is enabled.</p>
</td>
</tr>
<tr>
<td>
<code>logLevel</code><br/>
<em>
string
</em>
</td>
<td>
<p>LogLevel defines the log level of gateway api.</p>
</td>
</tr>
<tr>
<td>
<code>fgwLogLevel</code><br/>
<em>
string
</em>
</td>
<td>
<p>FGWLogLevel defines the log level of FGW.</p>
</td>
</tr>
<tr>
<td>
<code>StripAnyHostPort</code><br/>
<em>
bool
</em>
</td>
<td>
<p>StripAnyHostPort defines if strip any host port is enabled.</p>
</td>
</tr>
<tr>
<td>
<code>sslPassthroughUpstreamPort</code><br/>
<em>
int32
</em>
</td>
<td>
<p>SSLPassthroughUpstreamPort defines the default upstream port of SSL passthrough.</p>
</td>
</tr>
<tr>
<td>
<code>http1PerRequestLoadBalancing</code><br/>
<em>
bool
</em>
</td>
<td>
<p>HTTP1PerRequestLoadBalancing defines if load balancing based on per-request is enabled for http1.</p>
</td>
</tr>
<tr>
<td>
<code>http2PerRequestLoadBalancing</code><br/>
<em>
bool
</em>
</td>
<td>
<p>HTTP2PerRequestLoadBalancing defines if load balancing based on per-request is enabled for http2.</p>
</td>
</tr>
<tr>
<td>
<code>proxyTag</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.ProxyTag">
ProxyTag
</a>
</em>
</td>
<td>
<p>ProxyTag defines the proxy tag configuration of gateway api.</p>
</td>
</tr>
</tbody>
</table>
<h3 id="config.flomesh.io/v1alpha3.HTTP">HTTP
</h3>
<p>
(<em>Appears on:</em><a href="#config.flomesh.io/v1alpha3.IngressSpec">IngressSpec</a>)
</p>
<div>
<p>HTTP is the type to represent http.</p>
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
<code>enabled</code><br/>
<em>
bool
</em>
</td>
<td>
<p>Enabled defines if http is enabled.</p>
</td>
</tr>
<tr>
<td>
<code>bind</code><br/>
<em>
int32
</em>
</td>
<td>
<p>Bind defines the bind port of http.</p>
</td>
</tr>
<tr>
<td>
<code>listen</code><br/>
<em>
int32
</em>
</td>
<td>
<p>Listen defines the listen port of http.</p>
</td>
</tr>
<tr>
<td>
<code>nodePort</code><br/>
<em>
int32
</em>
</td>
<td>
<p>NodePort defines the node port of http.</p>
</td>
</tr>
</tbody>
</table>
<h3 id="config.flomesh.io/v1alpha3.ImageSpec">ImageSpec
</h3>
<p>
(<em>Appears on:</em><a href="#config.flomesh.io/v1alpha3.MeshConfigSpec">MeshConfigSpec</a>)
</p>
<div>
<p>ImageSpec is the type to represent image.</p>
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
<code>registry</code><br/>
<em>
string
</em>
</td>
<td>
<p>Registry defines the registry of docker image.</p>
</td>
</tr>
<tr>
<td>
<code>tag</code><br/>
<em>
string
</em>
</td>
<td>
<p>Tag defines the tag of docker image.</p>
</td>
</tr>
<tr>
<td>
<code>pullPolicy</code><br/>
<em>
<a href="https://v1-20.docs.kubernetes.io/docs/reference/generated/kubernetes-api/v1.20/#pullpolicy-v1-core">
Kubernetes core/v1.PullPolicy
</a>
</em>
</td>
<td>
<p>PullPolicy defines the pull policy of docker image.</p>
</td>
</tr>
</tbody>
</table>
<h3 id="config.flomesh.io/v1alpha3.IngressGatewayCertSpec">IngressGatewayCertSpec
</h3>
<p>
(<em>Appears on:</em><a href="#config.flomesh.io/v1alpha3.CertificateSpec">CertificateSpec</a>)
</p>
<div>
<p>IngressGatewayCertSpec is the type to represent the certificate specification for an ingress gateway.</p>
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
<code>subjectAltNames</code><br/>
<em>
[]string
</em>
</td>
<td>
<p>SubjectAltNames defines the Subject Alternative Names (domain names and IP addresses) secured by the certificate.</p>
</td>
</tr>
<tr>
<td>
<code>validityDuration</code><br/>
<em>
string
</em>
</td>
<td>
<p>ValidityDuration defines the validity duration of the certificate.</p>
</td>
</tr>
<tr>
<td>
<code>secret</code><br/>
<em>
<a href="https://v1-20.docs.kubernetes.io/docs/reference/generated/kubernetes-api/v1.20/#secretreference-v1-core">
Kubernetes core/v1.SecretReference
</a>
</em>
</td>
<td>
<p>Secret defines the secret in which the certificate is stored.</p>
</td>
</tr>
</tbody>
</table>
<h3 id="config.flomesh.io/v1alpha3.IngressSpec">IngressSpec
</h3>
<p>
(<em>Appears on:</em><a href="#config.flomesh.io/v1alpha3.MeshConfigSpec">MeshConfigSpec</a>)
</p>
<div>
<p>IngressSpec is the type to represent ingress.</p>
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
<code>enabled</code><br/>
<em>
bool
</em>
</td>
<td>
<p>Enabled defines if ingress is enabled.</p>
</td>
</tr>
<tr>
<td>
<code>namespaced</code><br/>
<em>
bool
</em>
</td>
<td>
<p>Namespaced defines if ingress is namespaced.</p>
</td>
</tr>
<tr>
<td>
<code>type</code><br/>
<em>
<a href="https://v1-20.docs.kubernetes.io/docs/reference/generated/kubernetes-api/v1.20/#servicetype-v1-core">
Kubernetes core/v1.ServiceType
</a>
</em>
</td>
<td>
<p>Type defines the type of ingress service.</p>
</td>
</tr>
<tr>
<td>
<code>logLevel</code><br/>
<em>
string
</em>
</td>
<td>
<p>LogLevel defines the log level of ingress.</p>
</td>
</tr>
<tr>
<td>
<code>http</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.HTTP">
HTTP
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>HTTP defines the http configuration of ingress.</p>
</td>
</tr>
<tr>
<td>
<code>tls</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.TLS">
TLS
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>TLS defines the tls configuration of ingress.</p>
</td>
</tr>
</tbody>
</table>
<h3 id="config.flomesh.io/v1alpha3.LocalDNSProxy">LocalDNSProxy
</h3>
<p>
(<em>Appears on:</em><a href="#config.flomesh.io/v1alpha3.SidecarSpec">SidecarSpec</a>)
</p>
<div>
<p>LocalDNSProxy is the type to represent FSM&rsquo;s local DNS proxy configuration.</p>
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
<code>enable</code><br/>
<em>
bool
</em>
</td>
<td>
<p>Enable defines a boolean indicating if the sidecars are enabled for local DNS Proxy.</p>
</td>
</tr>
<tr>
<td>
<code>primaryUpstreamDNSServerIPAddr</code><br/>
<em>
string
</em>
</td>
<td>
<em>(Optional)</em>
<p>PrimaryUpstreamDNSServerIPAddr defines a primary upstream DNS server for local DNS Proxy.</p>
</td>
</tr>
<tr>
<td>
<code>secondaryUpstreamDNSServerIPAddr</code><br/>
<em>
string
</em>
</td>
<td>
<em>(Optional)</em>
<p>SecondaryUpstreamDNSServerIPAddr defines a secondary upstream DNS server for local DNS Proxy.</p>
</td>
</tr>
<tr>
<td>
<code>wildcard</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.WildcardDN">
WildcardDN
</a>
</em>
</td>
<td>
<p>Wildcard defines Wildcard DN.</p>
</td>
</tr>
<tr>
<td>
<code>db</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.ResolveDN">
[]ResolveDN
</a>
</em>
</td>
<td>
<p>DB defines Resolve DB.</p>
</td>
</tr>
</tbody>
</table>
<h3 id="config.flomesh.io/v1alpha3.LocalProxyMode">LocalProxyMode
(<code>string</code> alias)</h3>
<p>
(<em>Appears on:</em><a href="#config.flomesh.io/v1alpha3.SidecarSpec">SidecarSpec</a>)
</p>
<div>
<p>LocalProxyMode is a type alias representing the way the sidecar proxies to the main application</p>
</div>
<table>
<thead>
<tr>
<th>Value</th>
<th>Description</th>
</tr>
</thead>
<tbody><tr><td><p>&#34;Localhost&#34;</p></td>
<td><p>LocalProxyModeLocalhost indicates the the sidecar should communicate with the main application over localhost</p>
</td>
</tr><tr><td><p>&#34;PodIP&#34;</p></td>
<td><p>LocalProxyModePodIP indicates that the sidecar should communicate with the main application via the pod ip</p>
</td>
</tr></tbody>
</table>
<h3 id="config.flomesh.io/v1alpha3.MeshConfig">MeshConfig
</h3>
<div>
<p>MeshConfig is the type used to represent the mesh configuration.</p>
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
<code>metadata</code><br/>
<em>
<a href="https://v1-20.docs.kubernetes.io/docs/reference/generated/kubernetes-api/v1.20/#objectmeta-v1-meta">
Kubernetes meta/v1.ObjectMeta
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Object&rsquo;s metadata.</p>
Refer to the Kubernetes API documentation for the fields of the
<code>metadata</code> field.
</td>
</tr>
<tr>
<td>
<code>spec</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.MeshConfigSpec">
MeshConfigSpec
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Spec is the MeshConfig specification.</p>
<br/>
<br/>
<table>
<tr>
<td>
<code>clusterSet</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.ClusterSetSpec">
ClusterSetSpec
</a>
</em>
</td>
<td>
<p>ClusterSetSpec defines the configurations of cluster.</p>
</td>
</tr>
<tr>
<td>
<code>sidecar</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.SidecarSpec">
SidecarSpec
</a>
</em>
</td>
<td>
<p>Sidecar defines the configurations of the proxy sidecar in a mesh.</p>
</td>
</tr>
<tr>
<td>
<code>repoServer</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.RepoServerSpec">
RepoServerSpec
</a>
</em>
</td>
<td>
<p>RepoServer defines the configurations of pipy repo server.</p>
</td>
</tr>
<tr>
<td>
<code>traffic</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.TrafficSpec">
TrafficSpec
</a>
</em>
</td>
<td>
<p>Traffic defines the traffic management configurations for a mesh instance.</p>
</td>
</tr>
<tr>
<td>
<code>observability</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.ObservabilitySpec">
ObservabilitySpec
</a>
</em>
</td>
<td>
<p>Observalility defines the observability configurations for a mesh instance.</p>
</td>
</tr>
<tr>
<td>
<code>certificate</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.CertificateSpec">
CertificateSpec
</a>
</em>
</td>
<td>
<p>Certificate defines the certificate management configurations for a mesh instance.</p>
</td>
</tr>
<tr>
<td>
<code>featureFlags</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.FeatureFlags">
FeatureFlags
</a>
</em>
</td>
<td>
<p>FeatureFlags defines the feature flags for a mesh instance.</p>
</td>
</tr>
<tr>
<td>
<code>pluginChains</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.PluginChainsSpec">
PluginChainsSpec
</a>
</em>
</td>
<td>
<p>PluginChains defines the default plugin chains.</p>
</td>
</tr>
<tr>
<td>
<code>ingress</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.IngressSpec">
IngressSpec
</a>
</em>
</td>
<td>
<p>Ingress defines the configurations of Ingress features.</p>
</td>
</tr>
<tr>
<td>
<code>gatewayAPI</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.GatewayAPISpec">
GatewayAPISpec
</a>
</em>
</td>
<td>
<p>GatewayAPI defines the configurations of GatewayAPI features.</p>
</td>
</tr>
<tr>
<td>
<code>serviceLB</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.ServiceLBSpec">
ServiceLBSpec
</a>
</em>
</td>
<td>
<p>ServiceLB defines the configurations of ServiceLBServiceLB features.</p>
</td>
</tr>
<tr>
<td>
<code>flb</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.FLBSpec">
FLBSpec
</a>
</em>
</td>
<td>
<p>FLB defines the configurations of FLB features.</p>
</td>
</tr>
<tr>
<td>
<code>egressGateway</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.EgressGatewaySpec">
EgressGatewaySpec
</a>
</em>
</td>
<td>
<p>EgressGateway defines the configurations of EgressGateway features.</p>
</td>
</tr>
<tr>
<td>
<code>image</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.ImageSpec">
ImageSpec
</a>
</em>
</td>
<td>
<p>Image defines the configurations of Image info</p>
</td>
</tr>
<tr>
<td>
<code>misc</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.MiscSpec">
MiscSpec
</a>
</em>
</td>
<td>
<p>Misc defines the configurations of misc info</p>
</td>
</tr>
</table>
</td>
</tr>
</tbody>
</table>
<h3 id="config.flomesh.io/v1alpha3.MeshConfigSpec">MeshConfigSpec
</h3>
<p>
(<em>Appears on:</em><a href="#config.flomesh.io/v1alpha3.MeshConfig">MeshConfig</a>)
</p>
<div>
<p>MeshConfigSpec is the spec for FSM&rsquo;s configuration.</p>
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
<code>clusterSet</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.ClusterSetSpec">
ClusterSetSpec
</a>
</em>
</td>
<td>
<p>ClusterSetSpec defines the configurations of cluster.</p>
</td>
</tr>
<tr>
<td>
<code>sidecar</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.SidecarSpec">
SidecarSpec
</a>
</em>
</td>
<td>
<p>Sidecar defines the configurations of the proxy sidecar in a mesh.</p>
</td>
</tr>
<tr>
<td>
<code>repoServer</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.RepoServerSpec">
RepoServerSpec
</a>
</em>
</td>
<td>
<p>RepoServer defines the configurations of pipy repo server.</p>
</td>
</tr>
<tr>
<td>
<code>traffic</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.TrafficSpec">
TrafficSpec
</a>
</em>
</td>
<td>
<p>Traffic defines the traffic management configurations for a mesh instance.</p>
</td>
</tr>
<tr>
<td>
<code>observability</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.ObservabilitySpec">
ObservabilitySpec
</a>
</em>
</td>
<td>
<p>Observalility defines the observability configurations for a mesh instance.</p>
</td>
</tr>
<tr>
<td>
<code>certificate</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.CertificateSpec">
CertificateSpec
</a>
</em>
</td>
<td>
<p>Certificate defines the certificate management configurations for a mesh instance.</p>
</td>
</tr>
<tr>
<td>
<code>featureFlags</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.FeatureFlags">
FeatureFlags
</a>
</em>
</td>
<td>
<p>FeatureFlags defines the feature flags for a mesh instance.</p>
</td>
</tr>
<tr>
<td>
<code>pluginChains</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.PluginChainsSpec">
PluginChainsSpec
</a>
</em>
</td>
<td>
<p>PluginChains defines the default plugin chains.</p>
</td>
</tr>
<tr>
<td>
<code>ingress</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.IngressSpec">
IngressSpec
</a>
</em>
</td>
<td>
<p>Ingress defines the configurations of Ingress features.</p>
</td>
</tr>
<tr>
<td>
<code>gatewayAPI</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.GatewayAPISpec">
GatewayAPISpec
</a>
</em>
</td>
<td>
<p>GatewayAPI defines the configurations of GatewayAPI features.</p>
</td>
</tr>
<tr>
<td>
<code>serviceLB</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.ServiceLBSpec">
ServiceLBSpec
</a>
</em>
</td>
<td>
<p>ServiceLB defines the configurations of ServiceLBServiceLB features.</p>
</td>
</tr>
<tr>
<td>
<code>flb</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.FLBSpec">
FLBSpec
</a>
</em>
</td>
<td>
<p>FLB defines the configurations of FLB features.</p>
</td>
</tr>
<tr>
<td>
<code>egressGateway</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.EgressGatewaySpec">
EgressGatewaySpec
</a>
</em>
</td>
<td>
<p>EgressGateway defines the configurations of EgressGateway features.</p>
</td>
</tr>
<tr>
<td>
<code>image</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.ImageSpec">
ImageSpec
</a>
</em>
</td>
<td>
<p>Image defines the configurations of Image info</p>
</td>
</tr>
<tr>
<td>
<code>misc</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.MiscSpec">
MiscSpec
</a>
</em>
</td>
<td>
<p>Misc defines the configurations of misc info</p>
</td>
</tr>
</tbody>
</table>
<h3 id="config.flomesh.io/v1alpha3.MeshRootCertificate">MeshRootCertificate
</h3>
<div>
<p>MeshRootCertificate defines the configuration for certificate issuing
by the mesh control plane</p>
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
<code>metadata</code><br/>
<em>
<a href="https://v1-20.docs.kubernetes.io/docs/reference/generated/kubernetes-api/v1.20/#objectmeta-v1-meta">
Kubernetes meta/v1.ObjectMeta
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Object&rsquo;s metadata</p>
Refer to the Kubernetes API documentation for the fields of the
<code>metadata</code> field.
</td>
</tr>
<tr>
<td>
<code>spec</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.MeshRootCertificateSpec">
MeshRootCertificateSpec
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Spec is the MeshRootCertificate config specification</p>
<br/>
<br/>
<table>
<tr>
<td>
<code>provider</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.ProviderSpec">
ProviderSpec
</a>
</em>
</td>
<td>
<p>Provider specifies the mesh certificate provider</p>
</td>
</tr>
<tr>
<td>
<code>trustDomain</code><br/>
<em>
string
</em>
</td>
<td>
<p>TrustDomain is the trust domain to use as a suffix in Common Names for new certificates.</p>
</td>
</tr>
</table>
</td>
</tr>
<tr>
<td>
<code>status</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.MeshRootCertificateStatus">
MeshRootCertificateStatus
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Status of the MeshRootCertificate resource</p>
</td>
</tr>
</tbody>
</table>
<h3 id="config.flomesh.io/v1alpha3.MeshRootCertificateSpec">MeshRootCertificateSpec
</h3>
<p>
(<em>Appears on:</em><a href="#config.flomesh.io/v1alpha3.MeshRootCertificate">MeshRootCertificate</a>)
</p>
<div>
<p>MeshRootCertificateSpec defines the mesh root certificate specification</p>
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
<code>provider</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.ProviderSpec">
ProviderSpec
</a>
</em>
</td>
<td>
<p>Provider specifies the mesh certificate provider</p>
</td>
</tr>
<tr>
<td>
<code>trustDomain</code><br/>
<em>
string
</em>
</td>
<td>
<p>TrustDomain is the trust domain to use as a suffix in Common Names for new certificates.</p>
</td>
</tr>
</tbody>
</table>
<h3 id="config.flomesh.io/v1alpha3.MeshRootCertificateStatus">MeshRootCertificateStatus
</h3>
<p>
(<em>Appears on:</em><a href="#config.flomesh.io/v1alpha3.MeshRootCertificate">MeshRootCertificate</a>)
</p>
<div>
<p>MeshRootCertificateStatus defines the status of the MeshRootCertificate resource</p>
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
<code>state</code><br/>
<em>
string
</em>
</td>
<td>
<p>State specifies the state of the certificate provider
All states are specified in constants.go</p>
</td>
</tr>
</tbody>
</table>
<h3 id="config.flomesh.io/v1alpha3.MiscSpec">MiscSpec
</h3>
<p>
(<em>Appears on:</em><a href="#config.flomesh.io/v1alpha3.MeshConfigSpec">MeshConfigSpec</a>)
</p>
<div>
<p>MiscSpec is the type to represent misc configs.</p>
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
<code>curlImage</code><br/>
<em>
string
</em>
</td>
<td>
<p>CurlImage defines the image of curl.</p>
</td>
</tr>
<tr>
<td>
<code>repoServerImage</code><br/>
<em>
string
</em>
</td>
<td>
<p>RepoServerImage defines the image of repo server.</p>
</td>
</tr>
</tbody>
</table>
<h3 id="config.flomesh.io/v1alpha3.ObservabilitySpec">ObservabilitySpec
</h3>
<p>
(<em>Appears on:</em><a href="#config.flomesh.io/v1alpha3.MeshConfigSpec">MeshConfigSpec</a>)
</p>
<div>
<p>ObservabilitySpec is the type to represent FSM&rsquo;s observability configurations.</p>
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
<code>fsmLogLevel</code><br/>
<em>
string
</em>
</td>
<td>
<p>FSMLogLevel defines the log level for FSM control plane logs.</p>
</td>
</tr>
<tr>
<td>
<code>enableDebugServer</code><br/>
<em>
bool
</em>
</td>
<td>
<p>EnableDebugServer defines if the debug endpoint on the FSM controller pod is enabled.</p>
</td>
</tr>
<tr>
<td>
<code>tracing</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.TracingSpec">
TracingSpec
</a>
</em>
</td>
<td>
<p>Tracing defines FSM&rsquo;s tracing configuration.</p>
</td>
</tr>
<tr>
<td>
<code>remoteLogging</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.RemoteLoggingSpec">
RemoteLoggingSpec
</a>
</em>
</td>
<td>
<p>RemoteLogging defines FSM&rsquo;s remote logging configuration.</p>
</td>
</tr>
</tbody>
</table>
<h3 id="config.flomesh.io/v1alpha3.PluginChainSpec">PluginChainSpec
</h3>
<p>
(<em>Appears on:</em><a href="#config.flomesh.io/v1alpha3.PluginChainsSpec">PluginChainsSpec</a>)
</p>
<div>
<p>PluginChainSpec is the type to represent plugin chain.</p>
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
<code>plugin</code><br/>
<em>
string
</em>
</td>
<td>
<p>Plugin defines the name of plugin</p>
</td>
</tr>
<tr>
<td>
<code>priority</code><br/>
<em>
float32
</em>
</td>
<td>
<p>Priority defines the priority of plugin</p>
</td>
</tr>
<tr>
<td>
<code>disable</code><br/>
<em>
bool
</em>
</td>
<td>
<p>Disable defines the visibility of plugin</p>
</td>
</tr>
</tbody>
</table>
<h3 id="config.flomesh.io/v1alpha3.PluginChainsSpec">PluginChainsSpec
</h3>
<p>
(<em>Appears on:</em><a href="#config.flomesh.io/v1alpha3.MeshConfigSpec">MeshConfigSpec</a>)
</p>
<div>
<p>PluginChainsSpec is the type to represent plugin chains.</p>
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
<code>inbound-tcp</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.PluginChainSpec">
[]PluginChainSpec
</a>
</em>
</td>
<td>
<p>InboundTCPChains defines inbound tcp chains</p>
</td>
</tr>
<tr>
<td>
<code>inbound-http</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.PluginChainSpec">
[]PluginChainSpec
</a>
</em>
</td>
<td>
<p>InboundHTTPChains defines inbound http chains</p>
</td>
</tr>
<tr>
<td>
<code>outbound-tcp</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.PluginChainSpec">
[]PluginChainSpec
</a>
</em>
</td>
<td>
<p>OutboundTCPChains defines outbound tcp chains</p>
</td>
</tr>
<tr>
<td>
<code>outbound-http</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.PluginChainSpec">
[]PluginChainSpec
</a>
</em>
</td>
<td>
<p>OutboundHTTPChains defines outbound http chains</p>
</td>
</tr>
</tbody>
</table>
<h3 id="config.flomesh.io/v1alpha3.ProviderSpec">ProviderSpec
</h3>
<p>
(<em>Appears on:</em><a href="#config.flomesh.io/v1alpha3.MeshRootCertificateSpec">MeshRootCertificateSpec</a>)
</p>
<div>
<p>ProviderSpec defines the certificate provider used by the mesh control plane</p>
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
<code>certManager</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.CertManagerProviderSpec">
CertManagerProviderSpec
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>CertManager specifies the cert-manager provider configuration</p>
</td>
</tr>
<tr>
<td>
<code>vault</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.VaultProviderSpec">
VaultProviderSpec
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Vault specifies the vault provider configuration</p>
</td>
</tr>
<tr>
<td>
<code>tresor</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.TresorProviderSpec">
TresorProviderSpec
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Tresor specifies the Tresor provider configuration</p>
</td>
</tr>
</tbody>
</table>
<h3 id="config.flomesh.io/v1alpha3.ProxyTag">ProxyTag
</h3>
<p>
(<em>Appears on:</em><a href="#config.flomesh.io/v1alpha3.GatewayAPISpec">GatewayAPISpec</a>)
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
<code>srcHostHeader</code><br/>
<em>
string
</em>
</td>
<td>
<p>SrcHostHeader defines the src host header.</p>
</td>
</tr>
<tr>
<td>
<code>dstHostHeader</code><br/>
<em>
string
</em>
</td>
<td>
<p>DstHostHeader defines the dst host header.</p>
</td>
</tr>
</tbody>
</table>
<h3 id="config.flomesh.io/v1alpha3.RemoteLoggingSpec">RemoteLoggingSpec
</h3>
<p>
(<em>Appears on:</em><a href="#config.flomesh.io/v1alpha3.ObservabilitySpec">ObservabilitySpec</a>)
</p>
<div>
<p>RemoteLoggingSpec is the type to represent FSM&rsquo;s remote logging configuration.</p>
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
<code>enable</code><br/>
<em>
bool
</em>
</td>
<td>
<p>Enable defines a boolean indicating if the sidecars are enabled for remote logging.</p>
</td>
</tr>
<tr>
<td>
<code>level</code><br/>
<em>
uint16
</em>
</td>
<td>
<p>Level defines the remote logging&rsquo;s level.</p>
</td>
</tr>
<tr>
<td>
<code>port</code><br/>
<em>
int16
</em>
</td>
<td>
<p>Port defines the remote logging&rsquo;s port.</p>
</td>
</tr>
<tr>
<td>
<code>address</code><br/>
<em>
string
</em>
</td>
<td>
<p>Address defines the remote logging&rsquo;s hostname.</p>
</td>
</tr>
<tr>
<td>
<code>endpoint</code><br/>
<em>
string
</em>
</td>
<td>
<p>Endpoint defines the API endpoint for remote logging requests sent to the collector.</p>
</td>
</tr>
<tr>
<td>
<code>authorization</code><br/>
<em>
string
</em>
</td>
<td>
<p>Authorization defines the access entity that allows to authorize someone in remote logging service.</p>
</td>
</tr>
<tr>
<td>
<code>sampledFraction</code><br/>
<em>
string
</em>
</td>
<td>
<p>SampledFraction defines the sampled fraction.</p>
</td>
</tr>
<tr>
<td>
<code>secretName</code><br/>
<em>
string
</em>
</td>
<td>
<p>SecretName defines the name of the secret that contains the configuration for remote logging.</p>
</td>
</tr>
</tbody>
</table>
<h3 id="config.flomesh.io/v1alpha3.RepoServerSpec">RepoServerSpec
</h3>
<p>
(<em>Appears on:</em><a href="#config.flomesh.io/v1alpha3.MeshConfigSpec">MeshConfigSpec</a>)
</p>
<div>
<p>RepoServerSpec is the type to represent repo server.</p>
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
<code>ipaddr</code><br/>
<em>
string
</em>
</td>
<td>
<p>IPAddr of the pipy repo server</p>
</td>
</tr>
<tr>
<td>
<code>port</code><br/>
<em>
int16
</em>
</td>
<td>
<p>Port defines the pipy repo server&rsquo;s port.</p>
</td>
</tr>
<tr>
<td>
<code>codebase</code><br/>
<em>
string
</em>
</td>
<td>
<p>Codebase is the folder used by fsmController</p>
</td>
</tr>
</tbody>
</table>
<h3 id="config.flomesh.io/v1alpha3.ResolveDN">ResolveDN
</h3>
<p>
(<em>Appears on:</em><a href="#config.flomesh.io/v1alpha3.LocalDNSProxy">LocalDNSProxy</a>)
</p>
<div>
<p>ResolveDN is the type to represent FSM&rsquo;s Resolve DN configuration.</p>
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
<code>dn</code><br/>
<em>
string
</em>
</td>
<td>
<p>DN defines resolve DN.</p>
</td>
</tr>
<tr>
<td>
<code>ipv4</code><br/>
<em>
[]string
</em>
</td>
<td>
<p>IPv4 defines a ipv4 address for resolve DN.</p>
</td>
</tr>
</tbody>
</table>
<h3 id="config.flomesh.io/v1alpha3.SSLPassthrough">SSLPassthrough
</h3>
<p>
(<em>Appears on:</em><a href="#config.flomesh.io/v1alpha3.TLS">TLS</a>)
</p>
<div>
<p>SSLPassthrough is the type to represent ssl passthrough.</p>
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
<code>enabled</code><br/>
<em>
bool
</em>
</td>
<td>
<p>Enabled defines if ssl passthrough is enabled.</p>
</td>
</tr>
<tr>
<td>
<code>upstreamPort</code><br/>
<em>
int32
</em>
</td>
<td>
<p>UpstreamPort defines the upstream port of ssl passthrough.</p>
</td>
</tr>
</tbody>
</table>
<h3 id="config.flomesh.io/v1alpha3.SecretKeyReferenceSpec">SecretKeyReferenceSpec
</h3>
<p>
(<em>Appears on:</em><a href="#config.flomesh.io/v1alpha3.VaultTokenSpec">VaultTokenSpec</a>)
</p>
<div>
<p>SecretKeyReferenceSpec defines the configuration of the secret reference</p>
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
<code>name</code><br/>
<em>
string
</em>
</td>
<td>
<p>Name specifies the name of the secret in which the Vault token is stored</p>
</td>
</tr>
<tr>
<td>
<code>key</code><br/>
<em>
string
</em>
</td>
<td>
<p>Key specifies the key whose value is the Vault token</p>
</td>
</tr>
<tr>
<td>
<code>namespace</code><br/>
<em>
string
</em>
</td>
<td>
<p>Namespace specifies the namespace of the secret in which the Vault token is stored</p>
</td>
</tr>
</tbody>
</table>
<h3 id="config.flomesh.io/v1alpha3.ServiceLBSpec">ServiceLBSpec
</h3>
<p>
(<em>Appears on:</em><a href="#config.flomesh.io/v1alpha3.MeshConfigSpec">MeshConfigSpec</a>)
</p>
<div>
<p>ServiceLBSpec is the type to represent service lb.</p>
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
<code>enabled</code><br/>
<em>
bool
</em>
</td>
<td>
<p>Enabled defines if service lb is enabled.</p>
</td>
</tr>
<tr>
<td>
<code>image</code><br/>
<em>
string
</em>
</td>
<td>
<p>Image defines the service lb image.</p>
</td>
</tr>
</tbody>
</table>
<h3 id="config.flomesh.io/v1alpha3.SidecarSpec">SidecarSpec
</h3>
<p>
(<em>Appears on:</em><a href="#config.flomesh.io/v1alpha3.MeshConfigSpec">MeshConfigSpec</a>)
</p>
<div>
<p>SidecarSpec is the type used to represent the specifications for the proxy sidecar.</p>
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
<code>enablePrivilegedInitContainer</code><br/>
<em>
bool
</em>
</td>
<td>
<p>EnablePrivilegedInitContainer defines a boolean indicating whether the init container for a meshed pod should run as privileged.</p>
</td>
</tr>
<tr>
<td>
<code>logLevel</code><br/>
<em>
string
</em>
</td>
<td>
<p>LogLevel defines the logging level for the sidecar&rsquo;s logs. Non developers should generally never set this value. In production environments the LogLevel should be set to error.</p>
</td>
</tr>
<tr>
<td>
<code>sidecarImage</code><br/>
<em>
string
</em>
</td>
<td>
<p>SidecarImage defines the container image used for the proxy sidecar.</p>
</td>
</tr>
<tr>
<td>
<code>sidecarDisabledMTLS</code><br/>
<em>
bool
</em>
</td>
<td>
<p>SidecarDisabledMTLS defines whether mTLS is disabled.</p>
</td>
</tr>
<tr>
<td>
<code>maxDataPlaneConnections</code><br/>
<em>
int
</em>
</td>
<td>
<p>MaxDataPlaneConnections defines the maximum allowed data plane connections from a proxy sidecar to the FSM controller.</p>
</td>
</tr>
<tr>
<td>
<code>configResyncInterval</code><br/>
<em>
string
</em>
</td>
<td>
<p>ConfigResyncInterval defines the resync interval for regular proxy broadcast updates.</p>
</td>
</tr>
<tr>
<td>
<code>sidecarTimeout</code><br/>
<em>
int
</em>
</td>
<td>
<p>SidecarTimeout defines the connect/idle/read/write timeout.</p>
</td>
</tr>
<tr>
<td>
<code>resources</code><br/>
<em>
<a href="https://v1-20.docs.kubernetes.io/docs/reference/generated/kubernetes-api/v1.20/#resourcerequirements-v1-core">
Kubernetes core/v1.ResourceRequirements
</a>
</em>
</td>
<td>
<p>Resources defines the compute resources for the sidecar.</p>
</td>
</tr>
<tr>
<td>
<code>tlsMinProtocolVersion</code><br/>
<em>
string
</em>
</td>
<td>
<p>TLSMinProtocolVersion defines the minimum TLS protocol version that the sidecar supports. Valid TLS protocol versions are TLS_AUTO, TLSv1_0, TLSv1_1, TLSv1_2 and TLSv1_3.</p>
</td>
</tr>
<tr>
<td>
<code>tlsMaxProtocolVersion</code><br/>
<em>
string
</em>
</td>
<td>
<p>TLSMaxProtocolVersion defines the maximum TLS protocol version that the sidecar supports. Valid TLS protocol versions are TLS_AUTO, TLSv1_0, TLSv1_1, TLSv1_2 and TLSv1_3.</p>
</td>
</tr>
<tr>
<td>
<code>cipherSuites</code><br/>
<em>
[]string
</em>
</td>
<td>
<p>CipherSuites defines a list of ciphers that listener supports when negotiating TLS 1.0-1.2. This setting has no effect when negotiating TLS 1.3. For valid cipher names, see the latest OpenSSL ciphers manual page. E.g. <a href="https://www.openssl.org/docs/man1.1.1/apps/ciphers.html">https://www.openssl.org/docs/man1.1.1/apps/ciphers.html</a>.</p>
</td>
</tr>
<tr>
<td>
<code>ecdhCurves</code><br/>
<em>
[]string
</em>
</td>
<td>
<p>ECDHCurves defines a list of ECDH curves that TLS connection supports. If not specified, the curves are [X25519, P-256] for non-FIPS build and P-256 for builds using BoringSSL FIPS.</p>
</td>
</tr>
<tr>
<td>
<code>localProxyMode</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.LocalProxyMode">
LocalProxyMode
</a>
</em>
</td>
<td>
<p>LocalProxyMode defines the network interface the proxy will use to send traffic to the backend service application. Acceptable values are [<code>Localhost</code>, <code>PodIP</code>]. The default is <code>Localhost</code></p>
</td>
</tr>
<tr>
<td>
<code>localDNSProxy</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.LocalDNSProxy">
LocalDNSProxy
</a>
</em>
</td>
<td>
<p>LocalDNSProxy improves the performance of your computer by caching the responses coming from your DNS servers</p>
</td>
</tr>
</tbody>
</table>
<h3 id="config.flomesh.io/v1alpha3.TLS">TLS
</h3>
<p>
(<em>Appears on:</em><a href="#config.flomesh.io/v1alpha3.IngressSpec">IngressSpec</a>)
</p>
<div>
<p>TLS is the type to represent tls.</p>
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
<code>enabled</code><br/>
<em>
bool
</em>
</td>
<td>
<p>Enabled defines if tls is enabled.</p>
</td>
</tr>
<tr>
<td>
<code>bind</code><br/>
<em>
int32
</em>
</td>
<td>
<p>Bind defines the bind port of tls.</p>
</td>
</tr>
<tr>
<td>
<code>listen</code><br/>
<em>
int32
</em>
</td>
<td>
<p>Listen defines the listen port of tls.</p>
</td>
</tr>
<tr>
<td>
<code>nodePort</code><br/>
<em>
int32
</em>
</td>
<td>
<p>NodePort defines the node port of tls.</p>
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
<p>MTLS defines if mTLS is enabled.</p>
</td>
</tr>
<tr>
<td>
<code>sslPassthrough</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.SSLPassthrough">
SSLPassthrough
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>SSLPassthrough defines the ssl passthrough configuration of tls.</p>
</td>
</tr>
</tbody>
</table>
<h3 id="config.flomesh.io/v1alpha3.TracingSpec">TracingSpec
</h3>
<p>
(<em>Appears on:</em><a href="#config.flomesh.io/v1alpha3.ObservabilitySpec">ObservabilitySpec</a>)
</p>
<div>
<p>TracingSpec is the type to represent FSM&rsquo;s tracing configuration.</p>
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
<code>enable</code><br/>
<em>
bool
</em>
</td>
<td>
<p>Enable defines a boolean indicating if the sidecars are enabled for tracing.</p>
</td>
</tr>
<tr>
<td>
<code>port</code><br/>
<em>
int16
</em>
</td>
<td>
<p>Port defines the tracing collector&rsquo;s port.</p>
</td>
</tr>
<tr>
<td>
<code>address</code><br/>
<em>
string
</em>
</td>
<td>
<p>Address defines the tracing collectio&rsquo;s hostname.</p>
</td>
</tr>
<tr>
<td>
<code>endpoint</code><br/>
<em>
string
</em>
</td>
<td>
<p>Endpoint defines the API endpoint for tracing requests sent to the collector.</p>
</td>
</tr>
<tr>
<td>
<code>sampledFraction</code><br/>
<em>
string
</em>
</td>
<td>
<p>SampledFraction defines the sampled fraction.</p>
</td>
</tr>
</tbody>
</table>
<h3 id="config.flomesh.io/v1alpha3.TrafficSpec">TrafficSpec
</h3>
<p>
(<em>Appears on:</em><a href="#config.flomesh.io/v1alpha3.MeshConfigSpec">MeshConfigSpec</a>)
</p>
<div>
<p>TrafficSpec is the type used to represent FSM&rsquo;s traffic management configuration.</p>
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
<code>interceptionMode</code><br/>
<em>
string
</em>
</td>
<td>
<p>InterceptionMode defines a string indicating which traffic interception mode is used.</p>
</td>
</tr>
<tr>
<td>
<code>enableEgress</code><br/>
<em>
bool
</em>
</td>
<td>
<p>EnableEgress defines a boolean indicating if mesh-wide Egress is enabled.</p>
</td>
</tr>
<tr>
<td>
<code>outboundIPRangeExclusionList</code><br/>
<em>
[]string
</em>
</td>
<td>
<p>OutboundIPRangeExclusionList defines a global list of IP address ranges to exclude from outbound traffic interception by the sidecar proxy.</p>
</td>
</tr>
<tr>
<td>
<code>outboundIPRangeInclusionList</code><br/>
<em>
[]string
</em>
</td>
<td>
<p>OutboundIPRangeInclusionList defines a global list of IP address ranges to include for outbound traffic interception by the sidecar proxy.
IP addresses outside this range will be excluded from outbound traffic interception by the sidecar proxy.</p>
</td>
</tr>
<tr>
<td>
<code>outboundPortExclusionList</code><br/>
<em>
[]int
</em>
</td>
<td>
<p>OutboundPortExclusionList defines a global list of ports to exclude from outbound traffic interception by the sidecar proxy.</p>
</td>
</tr>
<tr>
<td>
<code>inboundPortExclusionList</code><br/>
<em>
[]int
</em>
</td>
<td>
<p>InboundPortExclusionList defines a global list of ports to exclude from inbound traffic interception by the sidecar proxy.</p>
</td>
</tr>
<tr>
<td>
<code>enablePermissiveTrafficPolicyMode</code><br/>
<em>
bool
</em>
</td>
<td>
<p>EnablePermissiveTrafficPolicyMode defines a boolean indicating if permissive traffic policy mode is enabled mesh-wide.</p>
</td>
</tr>
<tr>
<td>
<code>serviceAccessMode</code><br/>
<em>
string
</em>
</td>
<td>
<p>ServiceAccessMode defines a string indicating service access mode.</p>
</td>
</tr>
<tr>
<td>
<code>inboundExternalAuthorization</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.ExternalAuthzSpec">
ExternalAuthzSpec
</a>
</em>
</td>
<td>
<p>InboundExternalAuthorization defines a ruleset that, if enabled, will configure a remote external authorization endpoint
for all inbound and ingress traffic in the mesh.</p>
</td>
</tr>
<tr>
<td>
<code>networkInterfaceExclusionList</code><br/>
<em>
[]string
</em>
</td>
<td>
<p>NetworkInterfaceExclusionList defines a global list of network interface
names to exclude from inbound and outbound traffic interception by the
sidecar proxy.</p>
</td>
</tr>
<tr>
<td>
<code>http1PerRequestLoadBalancing</code><br/>
<em>
bool
</em>
</td>
<td>
<p>HTTP1PerRequestLoadBalancing defines a boolean indicating if load balancing based on request is enabled for http1.</p>
</td>
</tr>
<tr>
<td>
<code>http2PerRequestLoadBalancing</code><br/>
<em>
bool
</em>
</td>
<td>
<p>HTTP1PerRequestLoadBalancing defines a boolean indicating if load balancing based on request is enabled for http2.</p>
</td>
</tr>
</tbody>
</table>
<h3 id="config.flomesh.io/v1alpha3.TresorCASpec">TresorCASpec
</h3>
<p>
(<em>Appears on:</em><a href="#config.flomesh.io/v1alpha3.TresorProviderSpec">TresorProviderSpec</a>)
</p>
<div>
<p>TresorCASpec defines the configuration of Tresor&rsquo;s root certificate</p>
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
<code>secretRef</code><br/>
<em>
<a href="https://v1-20.docs.kubernetes.io/docs/reference/generated/kubernetes-api/v1.20/#secretreference-v1-core">
Kubernetes core/v1.SecretReference
</a>
</em>
</td>
<td>
<p>SecretRef specifies the secret in which the root certificate is stored</p>
</td>
</tr>
</tbody>
</table>
<h3 id="config.flomesh.io/v1alpha3.TresorProviderSpec">TresorProviderSpec
</h3>
<p>
(<em>Appears on:</em><a href="#config.flomesh.io/v1alpha3.ProviderSpec">ProviderSpec</a>)
</p>
<div>
<p>TresorProviderSpec defines the configuration of the Tresor provider</p>
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
<code>ca</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.TresorCASpec">
TresorCASpec
</a>
</em>
</td>
<td>
<p>CA specifies Tresor&rsquo;s ca configuration</p>
</td>
</tr>
</tbody>
</table>
<h3 id="config.flomesh.io/v1alpha3.VaultProviderSpec">VaultProviderSpec
</h3>
<p>
(<em>Appears on:</em><a href="#config.flomesh.io/v1alpha3.ProviderSpec">ProviderSpec</a>)
</p>
<div>
<p>VaultProviderSpec defines the configuration of the Vault provider</p>
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
<code>host</code><br/>
<em>
string
</em>
</td>
<td>
<p>Host specifies the name of the Vault server</p>
</td>
</tr>
<tr>
<td>
<code>port</code><br/>
<em>
int
</em>
</td>
<td>
<p>Port specifies the port of the Vault server</p>
</td>
</tr>
<tr>
<td>
<code>role</code><br/>
<em>
string
</em>
</td>
<td>
<p>Role specifies the name of the role for use by mesh control plane</p>
</td>
</tr>
<tr>
<td>
<code>protocol</code><br/>
<em>
string
</em>
</td>
<td>
<p>Protocol specifies the protocol for connections to Vault</p>
</td>
</tr>
<tr>
<td>
<code>token</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.VaultTokenSpec">
VaultTokenSpec
</a>
</em>
</td>
<td>
<p>Token specifies the configuration of the token to be used by mesh control plane
to connect to Vault</p>
</td>
</tr>
</tbody>
</table>
<h3 id="config.flomesh.io/v1alpha3.VaultTokenSpec">VaultTokenSpec
</h3>
<p>
(<em>Appears on:</em><a href="#config.flomesh.io/v1alpha3.VaultProviderSpec">VaultProviderSpec</a>)
</p>
<div>
<p>VaultTokenSpec defines the configuration of the Vault token</p>
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
<code>secretKeyRef</code><br/>
<em>
<a href="#config.flomesh.io/v1alpha3.SecretKeyReferenceSpec">
SecretKeyReferenceSpec
</a>
</em>
</td>
<td>
<p>SecretKeyRef specifies the secret in which the Vault token is stored</p>
</td>
</tr>
</tbody>
</table>
<h3 id="config.flomesh.io/v1alpha3.WildcardDN">WildcardDN
</h3>
<p>
(<em>Appears on:</em><a href="#config.flomesh.io/v1alpha3.LocalDNSProxy">LocalDNSProxy</a>)
</p>
<div>
<p>WildcardDN is the type to represent FSM&rsquo;s Wildcard DN configuration.</p>
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
<code>enable</code><br/>
<em>
bool
</em>
</td>
<td>
<p>Enable defines a boolean indicating if wildcard are enabled for local DNS Proxy.</p>
</td>
</tr>
<tr>
<td>
<code>ipv4</code><br/>
<em>
[]string
</em>
</td>
<td>
<p>IPv4 defines a ipv4 address for wildcard DN.</p>
</td>
</tr>
</tbody>
</table>
<hr/>
<p><em>
Generated with <code>gen-crd-api-reference-docs</code>
on git commit <code>8abe9ab</code>.
</em></p>
