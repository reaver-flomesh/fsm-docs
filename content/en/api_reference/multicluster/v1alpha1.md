---
title: "MultiCluster v1alpha1 API Reference"
description: "MultiCluster v1alpha1 API reference documentation."
type: docs
---

<p>Packages:</p>
<ul>
<li>
<a href="#flomesh.io%2fv1alpha1">flomesh.io/v1alpha1</a>
</li>
</ul>
<h2 id="flomesh.io/v1alpha1">flomesh.io/v1alpha1</h2>
<div>
<p>Package v1alpha1 is the v1alpha1 version of the API.</p>
</div>
Resource Types:
<ul><li>
<a href="#flomesh.io/v1alpha1.Cluster">Cluster</a>
</li><li>
<a href="#flomesh.io/v1alpha1.GlobalTrafficPolicy">GlobalTrafficPolicy</a>
</li><li>
<a href="#flomesh.io/v1alpha1.ServiceExport">ServiceExport</a>
</li><li>
<a href="#flomesh.io/v1alpha1.ServiceImport">ServiceImport</a>
</li></ul>
<h3 id="flomesh.io/v1alpha1.Cluster">Cluster
</h3>
<div>
<p>Cluster is the Schema for the clusters API</p>
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
flomesh.io/v1alpha1
</code>
</td>
</tr>
<tr>
<td>
<code>kind</code><br/>
string
</td>
<td><code>Cluster</code></td>
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
<a href="#flomesh.io/v1alpha1.ClusterSpec">
ClusterSpec
</a>
</em>
</td>
<td>
<br/>
<br/>
<table>
<tr>
<td>
<code>region</code><br/>
<em>
string
</em>
</td>
<td>
<p>Region, the locality information of this cluster</p>
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
<p>Zone, the locality information of this cluster</p>
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
<p>Group, the locality information of this cluster</p>
</td>
</tr>
<tr>
<td>
<code>gatewayHost</code><br/>
<em>
string
</em>
</td>
<td>
<p>GatewayHost, the Full Qualified Domain Name or IP of the gateway/ingress of this cluster
If it&rsquo;s an IP address, only IPv4 is supported</p>
</td>
</tr>
<tr>
<td>
<code>gatewayPort</code><br/>
<em>
int32
</em>
</td>
<td>
<p>The port number of the gateway</p>
</td>
</tr>
<tr>
<td>
<code>kubeconfig</code><br/>
<em>
string
</em>
</td>
<td>
<p>Kubeconfig, The kubeconfig of the cluster you want to connnect to
This&rsquo;s not needed if ClusterMode is InCluster, it will use InCluster
config</p>
</td>
</tr>
<tr>
<td>
<code>fsmMeshConfigName</code><br/>
<em>
string
</em>
</td>
<td>
<em>(Optional)</em>
<p>FsmMeshConfigName, defines the name of the MeshConfig of managed cluster</p>
</td>
</tr>
<tr>
<td>
<code>fsmNamespace</code><br/>
<em>
string
</em>
</td>
<td>
<p>FsmNamespace, defines the namespace of managed cluster in which fsm is installed</p>
</td>
</tr>
</table>
</td>
</tr>
<tr>
<td>
<code>status</code><br/>
<em>
<a href="#flomesh.io/v1alpha1.ClusterStatus">
ClusterStatus
</a>
</em>
</td>
<td>
</td>
</tr>
</tbody>
</table>
<h3 id="flomesh.io/v1alpha1.GlobalTrafficPolicy">GlobalTrafficPolicy
</h3>
<div>
<p>GlobalTrafficPolicy is the Schema for the GlobalTrafficPolicys API</p>
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
flomesh.io/v1alpha1
</code>
</td>
</tr>
<tr>
<td>
<code>kind</code><br/>
string
</td>
<td><code>GlobalTrafficPolicy</code></td>
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
<a href="#flomesh.io/v1alpha1.GlobalTrafficPolicySpec">
GlobalTrafficPolicySpec
</a>
</em>
</td>
<td>
<br/>
<br/>
<table>
<tr>
<td>
<code>lbType</code><br/>
<em>
<a href="#flomesh.io/v1alpha1.LoadBalancerType">
LoadBalancerType
</a>
</em>
</td>
<td>
<p>Type of global load distribution</p>
</td>
</tr>
<tr>
<td>
<code>targets</code><br/>
<em>
<a href="#flomesh.io/v1alpha1.TrafficTarget">
[]TrafficTarget
</a>
</em>
</td>
<td>
<em>(Optional)</em>
</td>
</tr>
</table>
</td>
</tr>
<tr>
<td>
<code>status</code><br/>
<em>
<a href="#flomesh.io/v1alpha1.GlobalTrafficPolicyStatus">
GlobalTrafficPolicyStatus
</a>
</em>
</td>
<td>
</td>
</tr>
</tbody>
</table>
<h3 id="flomesh.io/v1alpha1.ServiceExport">ServiceExport
</h3>
<div>
<p>ServiceExport is the Schema for the ServiceExports API</p>
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
flomesh.io/v1alpha1
</code>
</td>
</tr>
<tr>
<td>
<code>kind</code><br/>
string
</td>
<td><code>ServiceExport</code></td>
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
<a href="#flomesh.io/v1alpha1.ServiceExportSpec">
ServiceExportSpec
</a>
</em>
</td>
<td>
<br/>
<br/>
<table>
<tr>
<td>
<code>pathRewrite</code><br/>
<em>
<a href="#flomesh.io/v1alpha1.PathRewrite">
PathRewrite
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>PathRewrite, it shares ONE rewrite rule for the same ServiceExport</p>
</td>
</tr>
<tr>
<td>
<code>sessionSticky</code><br/>
<em>
bool
</em>
</td>
<td>
<em>(Optional)</em>
<p>Indicates if session sticky is  enabled</p>
</td>
</tr>
<tr>
<td>
<code>loadBalancer</code><br/>
<em>
github.com/flomesh-io/fsm/pkg/apis.AlgoBalancer
</em>
</td>
<td>
<em>(Optional)</em>
<p>The LoadBalancer Type applied to the Ingress Rules those created by the ServiceExport</p>
</td>
</tr>
<tr>
<td>
<code>rules</code><br/>
<em>
<a href="#flomesh.io/v1alpha1.ServiceExportRule">
[]ServiceExportRule
</a>
</em>
</td>
<td>
<p>The paths for accessing the service via Ingress controller</p>
</td>
</tr>
<tr>
<td>
<code>targetClusters</code><br/>
<em>
[]string
</em>
</td>
<td>
<em>(Optional)</em>
<p>If empty, service is exported to all managed clusters.
If not empty, service is exported to specified clusters,
must be in format [region]/[zone]/[group]/[cluster]</p>
</td>
</tr>
<tr>
<td>
<code>serviceAccountName</code><br/>
<em>
string
</em>
</td>
<td>
<em>(Optional)</em>
<p>The ServiceAccount associated with this service</p>
</td>
</tr>
</table>
</td>
</tr>
<tr>
<td>
<code>status</code><br/>
<em>
<a href="#flomesh.io/v1alpha1.ServiceExportStatus">
ServiceExportStatus
</a>
</em>
</td>
<td>
</td>
</tr>
</tbody>
</table>
<h3 id="flomesh.io/v1alpha1.ServiceImport">ServiceImport
</h3>
<div>
<p>ServiceImport is the Schema for the ServiceImports API</p>
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
flomesh.io/v1alpha1
</code>
</td>
</tr>
<tr>
<td>
<code>kind</code><br/>
string
</td>
<td><code>ServiceImport</code></td>
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
<a href="#flomesh.io/v1alpha1.ServiceImportSpec">
ServiceImportSpec
</a>
</em>
</td>
<td>
<br/>
<br/>
<table>
<tr>
<td>
<code>ports</code><br/>
<em>
<a href="#flomesh.io/v1alpha1.ServicePort">
[]ServicePort
</a>
</em>
</td>
<td>
</td>
</tr>
<tr>
<td>
<code>ips</code><br/>
<em>
[]string
</em>
</td>
<td>
<em>(Optional)</em>
<p>ip will be used as the VIP for this service when type is ClusterSetIP.</p>
</td>
</tr>
<tr>
<td>
<code>type</code><br/>
<em>
<a href="#flomesh.io/v1alpha1.ServiceImportType">
ServiceImportType
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>type defines the type of this service.
Must be ClusterSetIP or Headless.</p>
</td>
</tr>
<tr>
<td>
<code>sessionAffinity</code><br/>
<em>
<a href="https://v1-26.docs.kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#serviceaffinity-v1-core">
Kubernetes core/v1.ServiceAffinity
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Supports &ldquo;ClientIP&rdquo; and &ldquo;None&rdquo;. Used to maintain session affinity.
Enable client IP based session affinity.
Must be ClientIP or None.
Defaults to None.
Ignored when type is Headless
More info: <a href="https://kubernetes.io/docs/concepts/services-networking/service/#virtual-ips-and-service-proxies">https://kubernetes.io/docs/concepts/services-networking/service/#virtual-ips-and-service-proxies</a></p>
</td>
</tr>
<tr>
<td>
<code>sessionAffinityConfig</code><br/>
<em>
<a href="https://v1-26.docs.kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#sessionaffinityconfig-v1-core">
Kubernetes core/v1.SessionAffinityConfig
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>sessionAffinityConfig contains session affinity configuration.</p>
</td>
</tr>
<tr>
<td>
<code>serviceAccountName</code><br/>
<em>
string
</em>
</td>
<td>
<em>(Optional)</em>
<p>The ServiceAccount associated with this service</p>
</td>
</tr>
</table>
</td>
</tr>
<tr>
<td>
<code>status</code><br/>
<em>
<a href="#flomesh.io/v1alpha1.ServiceImportStatus">
ServiceImportStatus
</a>
</em>
</td>
<td>
</td>
</tr>
</tbody>
</table>
<h3 id="flomesh.io/v1alpha1.ClusterConditionType">ClusterConditionType
(<code>string</code> alias)</h3>
<div>
<p>ClusterConditionType identifies a specific condition.</p>
</div>
<table>
<thead>
<tr>
<th>Value</th>
<th>Description</th>
</tr>
</thead>
<tbody><tr><td><p>&#34;Managed&#34;</p></td>
<td><p>ClusterManaged means that the cluster has joined the CLusterSet successfully
and is managed by Control Plane.</p>
</td>
</tr></tbody>
</table>
<h3 id="flomesh.io/v1alpha1.ClusterSpec">ClusterSpec
</h3>
<p>
(<em>Appears on:</em><a href="#flomesh.io/v1alpha1.Cluster">Cluster</a>)
</p>
<div>
<p>ClusterSpec defines the desired state of Cluster</p>
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
<code>region</code><br/>
<em>
string
</em>
</td>
<td>
<p>Region, the locality information of this cluster</p>
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
<p>Zone, the locality information of this cluster</p>
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
<p>Group, the locality information of this cluster</p>
</td>
</tr>
<tr>
<td>
<code>gatewayHost</code><br/>
<em>
string
</em>
</td>
<td>
<p>GatewayHost, the Full Qualified Domain Name or IP of the gateway/ingress of this cluster
If it&rsquo;s an IP address, only IPv4 is supported</p>
</td>
</tr>
<tr>
<td>
<code>gatewayPort</code><br/>
<em>
int32
</em>
</td>
<td>
<p>The port number of the gateway</p>
</td>
</tr>
<tr>
<td>
<code>kubeconfig</code><br/>
<em>
string
</em>
</td>
<td>
<p>Kubeconfig, The kubeconfig of the cluster you want to connnect to
This&rsquo;s not needed if ClusterMode is InCluster, it will use InCluster
config</p>
</td>
</tr>
<tr>
<td>
<code>fsmMeshConfigName</code><br/>
<em>
string
</em>
</td>
<td>
<em>(Optional)</em>
<p>FsmMeshConfigName, defines the name of the MeshConfig of managed cluster</p>
</td>
</tr>
<tr>
<td>
<code>fsmNamespace</code><br/>
<em>
string
</em>
</td>
<td>
<p>FsmNamespace, defines the namespace of managed cluster in which fsm is installed</p>
</td>
</tr>
</tbody>
</table>
<h3 id="flomesh.io/v1alpha1.ClusterStatus">ClusterStatus
</h3>
<p>
(<em>Appears on:</em><a href="#flomesh.io/v1alpha1.Cluster">Cluster</a>)
</p>
<div>
<p>ClusterStatus defines the observed state of Cluster</p>
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
</td>
</tr>
</tbody>
</table>
<h3 id="flomesh.io/v1alpha1.Endpoint">Endpoint
</h3>
<p>
(<em>Appears on:</em><a href="#flomesh.io/v1alpha1.ServicePort">ServicePort</a>)
</p>
<div>
<p>Endpoint represents a single logical &ldquo;backend&rdquo; implementing a service.</p>
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
<code>target</code><br/>
<em>
<a href="#flomesh.io/v1alpha1.Target">
Target
</a>
</em>
</td>
<td>
</td>
</tr>
<tr>
<td>
<code>clusterKey</code><br/>
<em>
string
</em>
</td>
<td>
</td>
</tr>
</tbody>
</table>
<h3 id="flomesh.io/v1alpha1.GlobalTrafficPolicySpec">GlobalTrafficPolicySpec
</h3>
<p>
(<em>Appears on:</em><a href="#flomesh.io/v1alpha1.GlobalTrafficPolicy">GlobalTrafficPolicy</a>)
</p>
<div>
<p>GlobalTrafficPolicySpec defines the desired state of GlobalTrafficPolicy</p>
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
<code>lbType</code><br/>
<em>
<a href="#flomesh.io/v1alpha1.LoadBalancerType">
LoadBalancerType
</a>
</em>
</td>
<td>
<p>Type of global load distribution</p>
</td>
</tr>
<tr>
<td>
<code>targets</code><br/>
<em>
<a href="#flomesh.io/v1alpha1.TrafficTarget">
[]TrafficTarget
</a>
</em>
</td>
<td>
<em>(Optional)</em>
</td>
</tr>
</tbody>
</table>
<h3 id="flomesh.io/v1alpha1.GlobalTrafficPolicyStatus">GlobalTrafficPolicyStatus
</h3>
<p>
(<em>Appears on:</em><a href="#flomesh.io/v1alpha1.GlobalTrafficPolicy">GlobalTrafficPolicy</a>)
</p>
<div>
<p>GlobalTrafficPolicyStatus defines the observed state of GlobalTrafficPolicy</p>
</div>
<h3 id="flomesh.io/v1alpha1.LoadBalancerType">LoadBalancerType
(<code>string</code> alias)</h3>
<p>
(<em>Appears on:</em><a href="#flomesh.io/v1alpha1.GlobalTrafficPolicySpec">GlobalTrafficPolicySpec</a>)
</p>
<div>
<p>LoadBalancerType defines the type of load balancer</p>
</div>
<table>
<thead>
<tr>
<th>Value</th>
<th>Description</th>
</tr>
</thead>
<tbody><tr><td><p>&#34;ActiveActive&#34;</p></td>
<td><p>ActiveActiveLbType is the type of load balancer that distributes traffic to all targets</p>
</td>
</tr><tr><td><p>&#34;FailOver&#34;</p></td>
<td><p>FailOverLbType is the type of load balancer that distributes traffic to the first available target</p>
</td>
</tr><tr><td><p>&#34;Locality&#34;</p></td>
<td><p>LocalityLbType is the type of load balancer that distributes traffic to targets in the same locality</p>
</td>
</tr></tbody>
</table>
<h3 id="flomesh.io/v1alpha1.PathRewrite">PathRewrite
</h3>
<p>
(<em>Appears on:</em><a href="#flomesh.io/v1alpha1.ServiceExportSpec">ServiceExportSpec</a>)
</p>
<div>
<p>PathRewrite defines the rewrite rule for service export</p>
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
<code>from</code><br/>
<em>
string
</em>
</td>
<td>
</td>
</tr>
<tr>
<td>
<code>to</code><br/>
<em>
string
</em>
</td>
<td>
</td>
</tr>
</tbody>
</table>
<h3 id="flomesh.io/v1alpha1.ServiceExportConditionType">ServiceExportConditionType
(<code>string</code> alias)</h3>
<div>
<p>ServiceExportConditionType identifies a specific condition.</p>
</div>
<table>
<thead>
<tr>
<th>Value</th>
<th>Description</th>
</tr>
</thead>
<tbody><tr><td><p>&#34;Conflict&#34;</p></td>
<td><p>ServiceExportConflict means that there is a conflict between two
exports for the same Service. When &ldquo;True&rdquo;, the condition message
should contain enough information to diagnose the conflict:
field(s) under contention, which cluster won, and why.
Users should not expect detailed per-cluster information in the
conflict message.</p>
</td>
</tr><tr><td><p>&#34;Valid&#34;</p></td>
<td><p>ServiceExportValid means that the service referenced by this
service export has been recognized as valid by controller.
This will be false if the service is found to be unexportable
(ExternalName, not found).</p>
</td>
</tr></tbody>
</table>
<h3 id="flomesh.io/v1alpha1.ServiceExportRule">ServiceExportRule
</h3>
<p>
(<em>Appears on:</em><a href="#flomesh.io/v1alpha1.ServiceExportSpec">ServiceExportSpec</a>)
</p>
<div>
<p>ServiceExportRule defines the rule for service export</p>
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
<code>portNumber</code><br/>
<em>
int32
</em>
</td>
<td>
<p>The port number of service</p>
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
<p>Path is matched against the path of an incoming request. Currently it can
contain characters disallowed from the conventional &ldquo;path&rdquo; part of a URL
as defined by RFC 3986. Paths must begin with a &lsquo;/&rsquo; and must be present
when using PathType with value &ldquo;Exact&rdquo; or &ldquo;Prefix&rdquo;.</p>
</td>
</tr>
<tr>
<td>
<code>pathType</code><br/>
<em>
<a href="https://v1-26.docs.kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#pathtype-v1-networking">
Kubernetes networking/v1.PathType
</a>
</em>
</td>
<td>
</td>
</tr>
</tbody>
</table>
<h3 id="flomesh.io/v1alpha1.ServiceExportSpec">ServiceExportSpec
</h3>
<p>
(<em>Appears on:</em><a href="#flomesh.io/v1alpha1.ServiceExport">ServiceExport</a>)
</p>
<div>
<p>ServiceExportSpec defines the desired state of ServiceExport</p>
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
<code>pathRewrite</code><br/>
<em>
<a href="#flomesh.io/v1alpha1.PathRewrite">
PathRewrite
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>PathRewrite, it shares ONE rewrite rule for the same ServiceExport</p>
</td>
</tr>
<tr>
<td>
<code>sessionSticky</code><br/>
<em>
bool
</em>
</td>
<td>
<em>(Optional)</em>
<p>Indicates if session sticky is  enabled</p>
</td>
</tr>
<tr>
<td>
<code>loadBalancer</code><br/>
<em>
github.com/flomesh-io/fsm/pkg/apis.AlgoBalancer
</em>
</td>
<td>
<em>(Optional)</em>
<p>The LoadBalancer Type applied to the Ingress Rules those created by the ServiceExport</p>
</td>
</tr>
<tr>
<td>
<code>rules</code><br/>
<em>
<a href="#flomesh.io/v1alpha1.ServiceExportRule">
[]ServiceExportRule
</a>
</em>
</td>
<td>
<p>The paths for accessing the service via Ingress controller</p>
</td>
</tr>
<tr>
<td>
<code>targetClusters</code><br/>
<em>
[]string
</em>
</td>
<td>
<em>(Optional)</em>
<p>If empty, service is exported to all managed clusters.
If not empty, service is exported to specified clusters,
must be in format [region]/[zone]/[group]/[cluster]</p>
</td>
</tr>
<tr>
<td>
<code>serviceAccountName</code><br/>
<em>
string
</em>
</td>
<td>
<em>(Optional)</em>
<p>The ServiceAccount associated with this service</p>
</td>
</tr>
</tbody>
</table>
<h3 id="flomesh.io/v1alpha1.ServiceExportStatus">ServiceExportStatus
</h3>
<p>
(<em>Appears on:</em><a href="#flomesh.io/v1alpha1.ServiceExport">ServiceExport</a>)
</p>
<div>
<p>ServiceExportStatus defines the observed state of ServiceExport</p>
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
</td>
</tr>
</tbody>
</table>
<h3 id="flomesh.io/v1alpha1.ServiceImportSpec">ServiceImportSpec
</h3>
<p>
(<em>Appears on:</em><a href="#flomesh.io/v1alpha1.ServiceImport">ServiceImport</a>)
</p>
<div>
<p>ServiceImportSpec describes an imported service and the information necessary to consume it.</p>
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
<code>ports</code><br/>
<em>
<a href="#flomesh.io/v1alpha1.ServicePort">
[]ServicePort
</a>
</em>
</td>
<td>
</td>
</tr>
<tr>
<td>
<code>ips</code><br/>
<em>
[]string
</em>
</td>
<td>
<em>(Optional)</em>
<p>ip will be used as the VIP for this service when type is ClusterSetIP.</p>
</td>
</tr>
<tr>
<td>
<code>type</code><br/>
<em>
<a href="#flomesh.io/v1alpha1.ServiceImportType">
ServiceImportType
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>type defines the type of this service.
Must be ClusterSetIP or Headless.</p>
</td>
</tr>
<tr>
<td>
<code>sessionAffinity</code><br/>
<em>
<a href="https://v1-26.docs.kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#serviceaffinity-v1-core">
Kubernetes core/v1.ServiceAffinity
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>Supports &ldquo;ClientIP&rdquo; and &ldquo;None&rdquo;. Used to maintain session affinity.
Enable client IP based session affinity.
Must be ClientIP or None.
Defaults to None.
Ignored when type is Headless
More info: <a href="https://kubernetes.io/docs/concepts/services-networking/service/#virtual-ips-and-service-proxies">https://kubernetes.io/docs/concepts/services-networking/service/#virtual-ips-and-service-proxies</a></p>
</td>
</tr>
<tr>
<td>
<code>sessionAffinityConfig</code><br/>
<em>
<a href="https://v1-26.docs.kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#sessionaffinityconfig-v1-core">
Kubernetes core/v1.SessionAffinityConfig
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>sessionAffinityConfig contains session affinity configuration.</p>
</td>
</tr>
<tr>
<td>
<code>serviceAccountName</code><br/>
<em>
string
</em>
</td>
<td>
<em>(Optional)</em>
<p>The ServiceAccount associated with this service</p>
</td>
</tr>
</tbody>
</table>
<h3 id="flomesh.io/v1alpha1.ServiceImportStatus">ServiceImportStatus
</h3>
<p>
(<em>Appears on:</em><a href="#flomesh.io/v1alpha1.ServiceImport">ServiceImport</a>)
</p>
<div>
<p>ServiceImportStatus describes derived state of an imported service.</p>
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
<code>clusters</code><br/>
<em>
<a href="#flomesh.io/v1alpha1.SourceStatus">
[]SourceStatus
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>clusters is the list of exporting clusters from which this service
was derived.</p>
</td>
</tr>
</tbody>
</table>
<h3 id="flomesh.io/v1alpha1.ServiceImportType">ServiceImportType
(<code>string</code> alias)</h3>
<p>
(<em>Appears on:</em><a href="#flomesh.io/v1alpha1.ServiceImportSpec">ServiceImportSpec</a>)
</p>
<div>
<p>ServiceImportType designates the type of a ServiceImport</p>
</div>
<table>
<thead>
<tr>
<th>Value</th>
<th>Description</th>
</tr>
</thead>
<tbody><tr><td><p>&#34;ClusterSetIP&#34;</p></td>
<td><p>ClusterSetIP are only accessible via the ClusterSet IP.</p>
</td>
</tr><tr><td><p>&#34;Headless&#34;</p></td>
<td><p>Headless services allow backend pods to be addressed directly.</p>
</td>
</tr></tbody>
</table>
<h3 id="flomesh.io/v1alpha1.ServicePort">ServicePort
</h3>
<p>
(<em>Appears on:</em><a href="#flomesh.io/v1alpha1.ServiceImportSpec">ServiceImportSpec</a>)
</p>
<div>
<p>ServicePort represents the port on which the service is exposed</p>
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
<em>(Optional)</em>
<p>The name of this port within the service. This must be a DNS_LABEL.
All ports within a ServiceSpec must have unique names. When considering
the endpoints for a Service, this must match the &lsquo;name&rsquo; field in the
EndpointPort.
Optional if only one ServicePort is defined on this service.</p>
</td>
</tr>
<tr>
<td>
<code>protocol</code><br/>
<em>
<a href="https://v1-26.docs.kubernetes.io/docs/reference/generated/kubernetes-api/v1.26/#protocol-v1-core">
Kubernetes core/v1.Protocol
</a>
</em>
</td>
<td>
<em>(Optional)</em>
<p>The IP protocol for this port. Supports &ldquo;TCP&rdquo;, &ldquo;UDP&rdquo;, and &ldquo;SCTP&rdquo;.
Default is TCP.</p>
</td>
</tr>
<tr>
<td>
<code>appProtocol</code><br/>
<em>
string
</em>
</td>
<td>
<em>(Optional)</em>
<p>The application protocol for this port.
This field follows standard Kubernetes label syntax.
Un-prefixed names are reserved for IANA standard service names (as per
RFC-6335 and <a href="http://www.iana.org/assignments/service-names)">http://www.iana.org/assignments/service-names)</a>.
Non-standard protocols should use prefixed names such as
mycompany.com/my-custom-protocol.
Field can be enabled with ServiceAppProtocol feature gate.</p>
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
<p>The port that will be exposed by this service.</p>
</td>
</tr>
<tr>
<td>
<code>endpoints</code><br/>
<em>
<a href="#flomesh.io/v1alpha1.Endpoint">
[]Endpoint
</a>
</em>
</td>
<td>
<p>The address of accessing the service</p>
</td>
</tr>
</tbody>
</table>
<h3 id="flomesh.io/v1alpha1.SourceStatus">SourceStatus
</h3>
<p>
(<em>Appears on:</em><a href="#flomesh.io/v1alpha1.ServiceImportStatus">ServiceImportStatus</a>)
</p>
<div>
<p>SourceStatus contains service configuration mapped to a specific source cluster</p>
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
<code>cluster</code><br/>
<em>
string
</em>
</td>
<td>
<p>cluster is the name of the exporting cluster. Must be a valid RFC-1123 DNS
label.</p>
</td>
</tr>
<tr>
<td>
<code>addresses</code><br/>
<em>
[]string
</em>
</td>
<td>
<p>in-cluster service, it&rsquo;s the cluster IPs
otherwise, it&rsquo;s the url of accessing that service in remote cluster
for example, http(s)://[Ingress IP/domain name]:[port]/[path]</p>
</td>
</tr>
</tbody>
</table>
<h3 id="flomesh.io/v1alpha1.Target">Target
</h3>
<p>
(<em>Appears on:</em><a href="#flomesh.io/v1alpha1.Endpoint">Endpoint</a>)
</p>
<div>
<p>Target represents a single logical &ldquo;backend&rdquo; implementing a service.</p>
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
</td>
</tr>
<tr>
<td>
<code>ip</code><br/>
<em>
string
</em>
</td>
<td>
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
</td>
</tr>
</tbody>
</table>
<h3 id="flomesh.io/v1alpha1.TrafficTarget">TrafficTarget
</h3>
<p>
(<em>Appears on:</em><a href="#flomesh.io/v1alpha1.GlobalTrafficPolicySpec">GlobalTrafficPolicySpec</a>)
</p>
<div>
<p>TrafficTarget defines the target of traffic</p>
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
<code>clusterKey</code><br/>
<em>
string
</em>
</td>
<td>
<p>Format: [region]/[zone]/[group]/[cluster]</p>
</td>
</tr>
<tr>
<td>
<code>weight</code><br/>
<em>
int
</em>
</td>
<td>
<em>(Optional)</em>
</td>
</tr>
</tbody>
</table>
<hr/>
<p><em>
Generated with <code>gen-crd-api-reference-docs</code>
on git commit <code>8abe9ab</code>.
</em></p>
