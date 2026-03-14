

# Operator CRD v2 参考

Package v2 - 本页提供 MinIO Operator `Operator CRD v2 Reference` CRD 的自动生成快速参考。
如需更完整的 MinIO Operator CRD 文档，请参阅
[MinIO Kubernetes Documentation](https://silo.pigsty.cc/operations/deployments/kubernetes.html)。  

`Operator CRD v2 Reference` API 随 MinIO Operator v4.0.0 一同发布。
MinIO Operator 会自动将使用 `/v1` API 的现有租户转换为 `/v2`。  

-   [Tenant](#tenant)

## Bucket

Bucket 描述默认创建的存储桶。

-   [TenantSpec](#tenantspec)

<table>
<colgroup>
<col style="width: 25%" />
<col style="width: 75%" />
</colgroup>
<thead>
<tr class="header">
<th style="text-align: left;">字段</th>
<th style="text-align: left;">说明</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;"><p><strong><code>name</code></strong>
<em>string</em></p></td>
<td style="text-align: left;"></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p><strong><code>region</code></strong>
<em>string</em></p></td>
<td style="text-align: left;"></td>
</tr>
<tr class="odd">
<td
style="text-align: left;"><p><strong><code>objectLock</code></strong>
<em>boolean</em></p></td>
<td style="text-align: left;"></td>
</tr>
</tbody>
</table>

## CertificateConfig

CertificateConfig (`certConfig`) 定义了在创建租户过程中由 Operator
自动生成的 TLS 证书的控制属性。
如果 `spec.autoCert: false`，这些字段不会生效。

-   [TenantSpec](#tenantspec)

<table>
<colgroup>
<col style="width: 25%" />
<col style="width: 75%" />
</colgroup>
<thead>
<tr class="header">
<th style="text-align: left;">字段</th>
<th style="text-align: left;">说明</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td
style="text-align: left;"><p><strong><code>commonName</code></strong>
<em>string</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>与自动生成的 TLS 证书关联的 <code>CommonName</code> 或 <code>CN</code>
属性。<br />
</p></td>
</tr>
<tr class="even">
<td
style="text-align: left;"><p><strong><code>organizationName</code></strong>
<em>string array</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>指定一个或多个与自动生成 TLS 证书关联的
<code>OrganizationName</code> 或 <code>O</code> 属性。<br />
</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p><strong><code>dnsNames</code></strong>
<em>string array</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>指定一个或多个与自动生成 TLS 证书关联的 x.509 Subject Alternative
Names (SAN)。MinIO Server Pod 会根据请求的主机名使用 SNI 判断应返回哪个证书。</p></td>
</tr>
</tbody>
</table>

## CertificateStatus

CertificateStatus 用于跟踪由 Operator 管理的所有证书。

-   [TenantStatus](#tenantstatus)

<table>
<colgroup>
<col style="width: 25%" />
<col style="width: 75%" />
</colgroup>
<thead>
<tr class="header">
<th style="text-align: left;">字段</th>
<th style="text-align: left;">说明</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td
style="text-align: left;"><p><strong><code>autoCertEnabled</code></strong>
<em>boolean</em></p></td>
<td style="text-align: left;"><p>AutoCertEnabled 用于记录我们是否已知该租户启用了 autocert。</p></td>
</tr>
<tr class="even">
<td
style="text-align: left;"><p><strong><code>customCertificates</code></strong>
<em><a
href="#customcertificates">CustomCertificates</a></em></p></td>
<td style="text-align: left;"><p>提供手动添加到 Operator 的
<code>client</code>、<code>minio</code> 和 <code>minioCAs</code>
自定义 TLS 证书的信息。</p></td>
</tr>
</tbody>
</table>

## CustomCertificateConfig

CustomCertificateConfig (`customCertificateConfig`) 提供在创建租户过程中
手动添加到 Operator 的 TLS 证书相关属性。
如果不存在自定义 TLS 证书，这些字段不包含数据。

-   [CustomCertificates](#customcertificates)

<table>
<colgroup>
<col style="width: 25%" />
<col style="width: 75%" />
</colgroup>
<thead>
<tr class="header">
<th style="text-align: left;">字段</th>
<th style="text-align: left;">说明</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;"><p><strong><code>certName</code></strong>
<em>string</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>输出与手动提供的 TLS 证书关联的一个或多个 <code>CertName</code>
属性。<br />
</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p><strong><code>domains</code></strong>
<em>string array</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>输出与手动提供的 TLS 证书关联的一个或多个 <code>Domains</code>
属性。<br />
</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p><strong><code>expiry</code></strong>
<em>string</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>输出与手动提供的 TLS 证书关联的一个或多个 <code>Expiry</code>
属性。<br />
</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p><strong><code>expiresIn</code></strong>
<em>string</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>输出与手动提供的 TLS 证书关联的一个或多个 <code>ExpiresIn</code>
属性。<br />
</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p><strong><code>serialNo</code></strong>
<em>string</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>输出与手动提供的 TLS 证书关联的一个或多个 <code>SerialNo</code>
属性。<br />
</p></td>
</tr>
</tbody>
</table>

## CustomCertificates

CustomCertificates (`customCertificates`) 提供在创建租户过程中
手动添加到 Operator 的 TLS 证书分组。
如果不存在自定义 TLS 证书，这些字段不包含数据。

-   [CertificateStatus](#certificatestatus)

<table>
<colgroup>
<col style="width: 25%" />
<col style="width: 75%" />
</colgroup>
<thead>
<tr class="header">
<th style="text-align: left;">字段</th>
<th style="text-align: left;">说明</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;"><p><strong><code>client</code></strong>
<em><a
href="#customcertificateconfig">CustomCertificateConfig</a>
array</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>客户端</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p><strong><code>minio</code></strong>
<em><a
href="#customcertificateconfig">CustomCertificateConfig</a>
array</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>MinIO</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p><strong><code>minioCAs</code></strong>
<em><a
href="#customcertificateconfig">CustomCertificateConfig</a>
array</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>证书颁发机构</p></td>
</tr>
</tbody>
</table>

## ExposeServices

ExposeServices (`exposeServices`) 定义 MinIO
对象存储和 Console 服务的暴露方式。  

-   [TenantSpec](#tenantspec)

<table>
<colgroup>
<col style="width: 25%" />
<col style="width: 75%" />
</colgroup>
<thead>
<tr class="header">
<th style="text-align: left;">字段</th>
<th style="text-align: left;">说明</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;"><p><strong><code>minio</code></strong>
<em>boolean</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>指示 Operator 暴露 MinIO 服务。默认为
<code>false</code>。<br />
</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p><strong><code>console</code></strong>
<em>boolean</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>指示 Operator 暴露 MinIO Console 服务。默认为
<code>false</code>。<br />
</p></td>
</tr>
</tbody>
</table>

## Features

Features (`features`) - 描述要在 MinIO 租户中启用或禁用哪些 MinIO 功能的对象。  

-   [TenantSpec](#tenantspec)

<table>
<colgroup>
<col style="width: 25%" />
<col style="width: 75%" />
</colgroup>
<thead>
<tr class="header">
<th style="text-align: left;">字段</th>
<th style="text-align: left;">说明</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;"><p><strong><code>bucketDNS</code></strong>
<em>boolean</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>指定 <code>true</code> 以允许客户端使用 DNS 路径
<code>&lt;bucket&gt;.minio.default.svc.cluster.local</code>
访问存储桶。默认为 <code>false</code>。</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p><strong><code>domains</code></strong>
<em><a
href="#tenantdomains">TenantDomains</a></em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>指定用于访问 MinIO 和 Console 的域名列表。</p></td>
</tr>
<tr class="odd">
<td
style="text-align: left;"><p><strong><code>enableSFTP</code></strong>
<em>boolean</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>以启用 SFTP 支持的方式启动 minio server。</p></td>
</tr>
</tbody>
</table>

## HealthStatus (string)

HealthStatus 表示租户是否处于健康、服务降级或离线状态。

-   [TenantStatus](#tenantstatus)

## KESConfig

KESConfig (`kes`) 定义作为 MinIO 租户一部分部署的
[MinIO Key Encryption Service](https://github.com/minio/kes) (KES)
StatefulSet 配置。
KES 支持使用外部密钥管理服务 (KMS)
对对象执行服务端加密。  

-   [TenantSpec](#tenantspec)

<table>
<colgroup>
<col style="width: 25%" />
<col style="width: 75%" />
</colgroup>
<thead>
<tr class="header">
<th style="text-align: left;">字段</th>
<th style="text-align: left;">说明</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;"><p><strong><code>replicas</code></strong>
<em>integer</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>指定要在租户中部署的 KES Pod 副本数量。
默认为 <code>2</code>。</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p><strong><code>image</code></strong>
<em>string</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p></td>
</tr>
<tr class="odd">
<td
style="text-align: left;"><p><strong><code>imagePullPolicy</code></strong>
<em><a
href="https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.23/#pullpolicy-v1-core">PullPolicy</a></em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>MinIO Docker 镜像的拉取策略。可指定以下值：<br />
</p>
<ul>
<li><p><code>Always</code><br />
</p></li>
<li><p><code>Never</code><br />
</p></li>
<li><p><code>IfNotPresent</code>（默认）<br />
</p></li>
</ul>
<p>详细信息请参阅 Kubernetes 文档：<a
href="https://kubernetes.io/docs/concepts/containers/images#updating-images">https://kubernetes.io/docs/concepts/containers/images#updating-images</a></p></td>
</tr>
<tr class="even">
<td
style="text-align: left;"><p><strong><code>serviceAccountName</code></strong>
<em>string</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>用于运行作为租户一部分创建的 MinIO KES Pod 的
<a
href="https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/">Kubernetes
Service Account</a>。<br />
</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p><strong><code>kesSecret</code></strong>
<em><a
href="https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.23/#localobjectreference-v1-core">LocalObjectReference</a></em></p></td>
<td style="text-align: left;"><p><strong>必填</strong><br />
</p>
<p>指定一个包含环境变量的 <a
href="https://kubernetes.io/docs/concepts/configuration/secret/">Kubernetes
opaque secret</a>，用于设置 MinIO KES 服务。<br />
</p>
<p>示例请参阅 <a
href="https://github.com/minio/operator/blob/master/examples/kes-secret.yaml">MinIO
Operator <code>console-secret.yaml</code></a>。</p></td>
</tr>
<tr class="even">
<td
style="text-align: left;"><p><strong><code>externalCertSecret</code></strong>
<em><a
href="#localcertificatereference">LocalCertificateReference</a></em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>为租户中的每个 MinIO KES Pod 启用带 SNI 支持的 TLS。
如果省略
<code>externalCertSecret</code> <strong>且</strong>
<code>spec.requestAutoCert</code> 设置为 <code>false</code>，
则 MinIO KES Pod 会在 <strong>未启用</strong> TLS 的情况下部署。<br />
</p>
<p>指定一个 <a
href="https://kubernetes.io/docs/concepts/configuration/secret/">Kubernetes
TLS secret</a>。MinIO Operator 会将指定证书复制到租户中的每个 MinIO Pod。
当 MinIO Pod/Service 响应 TLS 连接请求时，会使用 SNI 选择
<code>subjectAlternativeName</code> 匹配的证书。<br />
</p>
<p>指定一个包含以下字段的对象：<br />
</p>
<ul>
<li><p>- <code>name</code> - 包含 TLS 证书的 Kubernetes secret 名称。<br />
</p></li>
<li><p>- <code>type</code> - 指定
<code>kubernetes.io/tls</code><br />
</p></li>
</ul>
<p>有关为 MinIO 租户配置 TLS 的示例和更完整文档，
请参阅 <a
href="https://silo.pigsty.cc/reference/operator-crd.html">MinIO
Operator CRD</a> 参考。</p></td>
</tr>
<tr class="odd">
<td
style="text-align: left;"><p><strong><code>clientCertSecret</code></strong>
<em><a
href="#localcertificatereference">LocalCertificateReference</a></em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>指定一个包含自定义根证书颁发机构和 x.509 证书的 <a
href="https://kubernetes.io/docs/concepts/configuration/secret/">Kubernetes
TLS secret</a>，用于与外部 Key Management Service
（例如 Hashicorp Vault）执行 mTLS 认证。<br />
</p>
<p>指定一个包含以下字段的对象：<br />
</p>
<ul>
<li><p>- <code>name</code> - 包含证书颁发机构和 x.509 证书的 Kubernetes secret 名称。<br />
</p></li>
<li><p>- <code>type</code> - 指定
<code>kubernetes.io/tls</code><br />
</p></li>
</ul></td>
</tr>
<tr class="even">
<td
style="text-align: left;"><p><strong><code>gcpCredentialSecretName</code></strong>
<em>string</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<pre><code>指定 KES 用于向 GCP key store 进行身份验证的 GCP 默认凭证</code></pre></td>
</tr>
<tr class="odd">
<td
style="text-align: left;"><p><strong><code>gcpWorkloadIdentityPool</code></strong>
<em>string</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<pre><code>指定 workload identity pool 名称（生成 service account token 时必需）</code></pre></td>
</tr>
<tr class="even">
<td
style="text-align: left;"><p><strong><code>annotations</code></strong>
<em>object (keys:string, values:string)</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>如果提供，则将这些 annotations 用于 KES 对象元数据注解。</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p><strong><code>labels</code></strong>
<em>object (keys:string, values:string)</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>如果提供，则将这些 labels 用于 KES 对象元数据标签。</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p><strong><code>resources</code></strong>
<em><a
href="https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.23/#resourcerequirements-v1-core">ResourceRequirements</a></em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>用于在 MinIO 租户中指定 CPU 和内存
<a
href="https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/">resource
allocations</a> 或限制的对象规范。<br />
</p></td>
</tr>
<tr class="odd">
<td
style="text-align: left;"><p><strong><code>nodeSelector</code></strong>
<em>object (keys:string, values:string)</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>Operator 在选择要部署 MinIO KES Pod 的节点时应用的筛选条件。
Operator 仅会选择标签与指定 selector 匹配的节点。<br />
</p>
<p>更多信息请参阅 Kubernetes 文档中的 <a
href="https://kubernetes.io/docs/concepts/configuration/assign-pod-node/">Assigning
Pods to Nodes</a>。</p></td>
</tr>
<tr class="even">
<td
style="text-align: left;"><p><strong><code>tolerations</code></strong>
<em><a
href="https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.23/#toleration-v1-core">Toleration</a>
array</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>指定一个或多个要应用到 MinIO KES Pod 的 <a
href="https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/">Kubernetes
tolerations</a>。</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p><strong><code>affinity</code></strong>
<em><a
href="https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.23/#affinity-v1-core">Affinity</a></em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>为 KES Pod 指定 node affinity、pod affinity 和 pod anti-affinity。<br />
</p></td>
</tr>
<tr class="even">
<td
style="text-align: left;"><p><strong><code>topologySpreadConstraints</code></strong>
<em><a
href="https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.23/#topologyspreadconstraint-v1-core">TopologySpreadConstraint</a>
array</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>指定一个或多个要应用到 MinIO 池中部署 Pod 的 <a
href="https://kubernetes.io/docs/concepts/workloads/pods/pod-topology-spread-constraints/">Kubernetes
Topology Spread Constraints</a>。</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p><strong><code>keyName</code></strong>
<em>string</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>如果提供，则将其用作 KES 在 KMS 后端创建的密钥名称。</p></td>
</tr>
<tr class="even">
<td
style="text-align: left;"><p><strong><code>securityContext</code></strong>
<em><a
href="https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.23/#podsecuritycontext-v1-core">PodSecurityContext</a></em></p></td>
<td style="text-align: left;"><p>指定 MinIO KES Pod 的 <a
href="https://kubernetes.io/docs/tasks/configure-pod-container/security-context/">Security
Context</a>。
Operator 仅支持以下 Pod 安全字段：<br />
</p>
<ul>
<li><p><code>fsGroup</code><br />
</p></li>
<li><p><code>fsGroupChangePolicy</code><br />
</p></li>
<li><p><code>runAsGroup</code><br />
</p></li>
<li><p><code>runAsNonRoot</code><br />
</p></li>
<li><p><code>runAsUser</code><br />
</p></li>
<li><p><code>seLinuxOptions</code><br />
</p></li>
</ul></td>
</tr>
<tr class="odd">
<td
style="text-align: left;"><p><strong><code>containerSecurityContext</code></strong>
<em><a
href="https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.23/#securitycontext-v1-core">SecurityContext</a></em></p></td>
<td style="text-align: left;"><p>指定 MinIO KES Pod 的 <a
href="https://kubernetes.io/docs/tasks/configure-pod-container/security-context/">Security
Context</a>。</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p><strong><code>env</code></strong>
<em><a
href="https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.23/#envvar-v1-core">EnvVar</a>
array</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>如果提供，MinIO Operator 在部署 KES 资源时会添加指定的环境变量。</p></td>
</tr>
</tbody>
</table>

## LocalCertificateReference

LocalCertificateReference (`externalCertSecret`,
`externalCaCertSecret`,`clientCertSecret`) 包含一个 Kubernetes secret，
其中存放用于在 MinIO 租户中启用 TLS 的 TLS 证书或证书颁发机构文件。  

-   [KESConfig](#kesconfig)

-   [TenantSpec](#tenantspec)

<table>
<colgroup>
<col style="width: 25%" />
<col style="width: 75%" />
</colgroup>
<thead>
<tr class="header">
<th style="text-align: left;">字段</th>
<th style="text-align: left;">说明</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;"><p><strong><code>name</code></strong>
<em>string</em></p></td>
<td style="text-align: left;"><p><strong>必填</strong><br />
</p>
<p>包含 TLS 证书或证书颁发机构文件的 Kubernetes secret 名称。<br />
</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p><strong><code>type</code></strong>
<em>string</em></p></td>
<td style="text-align: left;"><p><strong>必填</strong><br />
</p>
<p>Kubernetes secret 的类型。指定
<code>kubernetes.io/tls</code><br />
</p></td>
</tr>
</tbody>
</table>

## Logging

Logging 描述 MinIO 租户的日志记录。

-   [TenantSpec](#tenantspec)

<table>
<colgroup>
<col style="width: 25%" />
<col style="width: 75%" />
</colgroup>
<thead>
<tr class="header">
<th style="text-align: left;">字段</th>
<th style="text-align: left;">说明</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;"><p><strong><code>json</code></strong>
<em>boolean</em></p></td>
<td style="text-align: left;"></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p><strong><code>anonymous</code></strong>
<em>boolean</em></p></td>
<td style="text-align: left;"></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p><strong><code>quiet</code></strong>
<em>boolean</em></p></td>
<td style="text-align: left;"></td>
</tr>
</tbody>
</table>

## Pool

Pool (`pools`) 定义租户上的一个 MinIO server pool。
每个 pool 由一组 MinIO server Pod 组成，
这些 Pod 通过“池化”其存储资源来支持对象存储和检索请求。
每个 server pool 都独立于其他 pool，
并支持对 MinIO 租户中可用存储资源进行水平扩展。  

有关 `pools` 对象的示例和更完整文档，请参阅
[MinIO Operator CRD](https://silo.pigsty.cc/reference/operator-crd.html) 参考。  

-   [TenantSpec](#tenantspec)

<table>
<colgroup>
<col style="width: 25%" />
<col style="width: 75%" />
</colgroup>
<thead>
<tr class="header">
<th style="text-align: left;">字段</th>
<th style="text-align: left;">说明</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;"><p><strong><code>name</code></strong>
<em>string</em></p></td>
<td style="text-align: left;"><p><strong>必填</strong> 指定 pool 名称。
如果省略此字段，Operator 会自动生成 pool 名称。</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p><strong><code>servers</code></strong>
<em>integer</em></p></td>
<td style="text-align: left;"><p><strong>必填</strong></p>
<p>要在该 pool 中部署的 MinIO server Pod 数量。最小值为 <code>2</code>。</p>
<p>MinIO Operator 要求每个 pool 至少有 <code>4</code> 个卷。
具体来说，
<code>pools.servers X pools.volumesPerServer</code> 的结果必须大于
<code>4</code>。<br />
</p></td>
</tr>
<tr class="odd">
<td
style="text-align: left;"><p><strong><code>volumesPerServer</code></strong>
<em>integer</em></p></td>
<td style="text-align: left;"><p><strong>必填</strong><br />
</p>
<p>为该 pool 中每个 MinIO server Pod 生成的 Persistent Volume Claim 数量。<br />
</p>
<p>MinIO Operator 要求每个 pool 至少有 <code>4</code> 个卷。
具体来说，
<code>pools.servers X pools.volumesPerServer</code> 的结果必须大于
<code>4</code>。<br />
</p></td>
</tr>
<tr class="even">
<td
style="text-align: left;"><p><strong><code>volumeClaimTemplate</code></strong>
<em><a
href="https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.23/#persistentvolumeclaim-v1-core">PersistentVolumeClaim</a></em></p></td>
<td style="text-align: left;"><p><strong>必填</strong><br />
</p>
<p>指定 MinIO Operator 为 MinIO 租户生成 Persistent Volume Claim
时使用的配置选项。<br />
</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p><strong><code>resources</code></strong>
<em><a
href="https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.23/#resourcerequirements-v1-core">ResourceRequirements</a></em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>用于在 MinIO 租户中指定 CPU 和内存
<a
href="https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/">resource
allocations</a> 或限制的对象规范。<br />
</p></td>
</tr>
<tr class="even">
<td
style="text-align: left;"><p><strong><code>nodeSelector</code></strong>
<em>object (keys:string, values:string)</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>Operator 在选择要在哪些节点部署该 pool Pod 时应用的筛选条件。
Operator 仅会选择标签与指定 selector 匹配的节点。<br />
</p>
<p>更多信息请参阅 Kubernetes 文档中的 <a
href="https://kubernetes.io/docs/concepts/configuration/assign-pod-node/">Assigning
Pods to Nodes</a>。</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p><strong><code>affinity</code></strong>
<em><a
href="https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.23/#affinity-v1-core">Affinity</a></em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>为 MinIO pool 中的 Pod 指定 node affinity、pod affinity 和 pod anti-affinity。<br />
</p></td>
</tr>
<tr class="even">
<td
style="text-align: left;"><p><strong><code>tolerations</code></strong>
<em><a
href="https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.23/#toleration-v1-core">Toleration</a>
array</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>指定一个或多个要应用到 MinIO pool 中部署 Pod 的 <a
href="https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/">Kubernetes
tolerations</a>。</p></td>
</tr>
<tr class="odd">
<td
style="text-align: left;"><p><strong><code>topologySpreadConstraints</code></strong>
<em><a
href="https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.23/#topologyspreadconstraint-v1-core">TopologySpreadConstraint</a>
array</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>指定一个或多个要应用到 MinIO pool 中部署 Pod 的 <a
href="https://kubernetes.io/docs/concepts/workloads/pods/pod-topology-spread-constraints/">Kubernetes
Topology Spread Constraints</a>。</p></td>
</tr>
<tr class="even">
<td
style="text-align: left;"><p><strong><code>securityContext</code></strong>
<em><a
href="https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.23/#podsecuritycontext-v1-core">PodSecurityContext</a></em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>指定 pool 中 Pod 的 <a
href="https://kubernetes.io/docs/tasks/configure-pod-container/security-context/">Security
Context</a>。
Operator 仅支持以下 Pod 安全字段：<br />
</p>
<ul>
<li><p><code>fsGroup</code><br />
</p></li>
<li><p><code>fsGroupChangePolicy</code><br />
</p></li>
<li><p><code>runAsGroup</code><br />
</p></li>
<li><p><code>runAsNonRoot</code><br />
</p></li>
<li><p><code>runAsUser</code><br />
</p></li>
</ul></td>
</tr>
<tr class="odd">
<td
style="text-align: left;"><p><strong><code>containerSecurityContext</code></strong>
<em><a
href="https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.23/#securitycontext-v1-core">SecurityContext</a></em></p></td>
<td style="text-align: left;"><p>指定 pool 中容器的 <a
href="https://kubernetes.io/docs/tasks/configure-pod-container/security-context/">Security
Context</a>。
Operator 仅支持以下容器安全字段：<br />
</p>
<ul>
<li><p><code>runAsGroup</code><br />
</p></li>
<li><p><code>runAsNonRoot</code><br />
</p></li>
<li><p><code>runAsUser</code><br />
</p></li>
</ul></td>
</tr>
<tr class="even">
<td
style="text-align: left;"><p><strong><code>annotations</code></strong>
<em>object (keys:string, values:string)</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>指定要追加到 Pool 的自定义 labels 和 annotations。
<strong>可选</strong><br />
</p>
<p>如果提供，则将这些 annotations 用于 Pool 对象元数据注解
（StatefulSet 和 Pod 模板）。</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p><strong><code>labels</code></strong>
<em>object (keys:string, values:string)</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>如果提供，则将这些 labels 用于 Pool 对象元数据标签
（StatefulSet 和 Pod 模板）。</p></td>
</tr>
<tr class="even">
<td
style="text-align: left;"><p><strong><code>runtimeClassName</code></strong>
<em>string</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>如果提供，StatefulSet 中的每个 Pod 都将使用指定的 RuntimeClassName 运行。
更多信息请参阅 <a
href="https://kubernetes.io/docs/concepts/containers/runtime-class/">https://kubernetes.io/docs/concepts/containers/runtime-class/</a></p></td>
</tr>
</tbody>
</table>

## PoolState (string)

PoolState 表示 pool 的状态。

-   [PoolStatus](#poolstatus)

## PoolStatus

PoolStatus 用于跟踪所有 pool 及其当前状态。

-   [TenantStatus](#tenantstatus)

<table>
<colgroup>
<col style="width: 25%" />
<col style="width: 75%" />
</colgroup>
<thead>
<tr class="header">
<th style="text-align: left;">字段</th>
<th style="text-align: left;">说明</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;"><p><strong><code>ssName</code></strong>
<em>string</em></p></td>
<td style="text-align: left;"></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p><strong><code>state</code></strong>
<em><a
href="#poolstate">PoolState</a></em></p></td>
<td style="text-align: left;"></td>
</tr>
<tr class="odd">
<td
style="text-align: left;"><p><strong><code>legacySecurityContext</code></strong>
<em>boolean</em></p></td>
<td style="text-align: left;"><p>LegacySecurityContext 表示旧版
SecurityContext。它表示该 pool 创建于 v4.2.3 之前，
当时我们引入了默认的非 root securityContext，
因此应继续在不设置 Security Context 的情况下运行此 pool。</p></td>
</tr>
</tbody>
</table>

## PoolsMetadata

PoolsMetadata (`poolsMetadata`) 为 MinIO pool statefulset / pod
定义自定义 labels 和 annotations。  

-   [TenantSpec](#tenantspec)

<table>
<colgroup>
<col style="width: 25%" />
<col style="width: 75%" />
</colgroup>
<thead>
<tr class="header">
<th style="text-align: left;">字段</th>
<th style="text-align: left;">说明</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;"><p><strong><code>labels</code></strong>
<em>object (keys:string, values:string)</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>如果提供，则将这些 labels 追加到 MinIO statefulset / pod。</p></td>
</tr>
<tr class="even">
<td
style="text-align: left;"><p><strong><code>annotations</code></strong>
<em>object (keys:string, values:string)</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>如果提供，则将这些 annotations 追加到 MinIO statefulset / pod。</p></td>
</tr>
</tbody>
</table>

## ServiceMetadata

ServiceMetadata (`serviceMetadata`) 为 MinIO Object Storage 服务
和/或 MinIO Console 服务定义自定义 labels 和 annotations。  

-   [TenantSpec](#tenantspec)

<table>
<colgroup>
<col style="width: 25%" />
<col style="width: 75%" />
</colgroup>
<thead>
<tr class="header">
<th style="text-align: left;">字段</th>
<th style="text-align: left;">说明</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td
style="text-align: left;"><p><strong><code>minioServiceLabels</code></strong>
<em>object (keys:string, values:string)</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>如果提供，则将这些 labels 追加到 MinIO 服务。</p></td>
</tr>
<tr class="even">
<td
style="text-align: left;"><p><strong><code>minioServiceAnnotations</code></strong>
<em>object (keys:string, values:string)</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>如果提供，则将这些 annotations 追加到 MinIO 服务。</p></td>
</tr>
<tr class="odd">
<td
style="text-align: left;"><p><strong><code>consoleServiceLabels</code></strong>
<em>object (keys:string, values:string)</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>如果提供，则将这些 labels 追加到 Console 服务。</p></td>
</tr>
<tr class="even">
<td
style="text-align: left;"><p><strong><code>consoleServiceAnnotations</code></strong>
<em>object (keys:string, values:string)</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>如果提供，则将这些 annotations 追加到 Console 服务。</p></td>
</tr>
<tr class="odd">
<td
style="text-align: left;"><p><strong><code>kesServiceLabels</code></strong>
<em>object (keys:string, values:string)</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>如果提供，则将这些 labels 追加到 KES 服务。</p></td>
</tr>
<tr class="even">
<td
style="text-align: left;"><p><strong><code>kesServiceAnnotations</code></strong>
<em>object (keys:string, values:string)</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>如果提供，则将这些 annotations 追加到 KES 服务。</p></td>
</tr>
</tbody>
</table>

## SideCars

SideCars (`sidecars`) 定义了一个容器列表，
Operator 会将其附加到 `pool` 中的每个 MinIO server Pod。

-   [TenantSpec](#tenantspec)

<table>
<colgroup>
<col style="width: 25%" />
<col style="width: 75%" />
</colgroup>
<thead>
<tr class="header">
<th style="text-align: left;">字段</th>
<th style="text-align: left;">说明</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td
style="text-align: left;"><p><strong><code>containers</code></strong>
<em><a
href="https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.23/#container-v1-core">Container</a>
array</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>要在 Pod 内运行的容器列表。</p></td>
</tr>
<tr class="even">
<td
style="text-align: left;"><p><strong><code>volumeClaimTemplates</code></strong>
<em><a
href="https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.23/#persistentvolumeclaim-v1-core">PersistentVolumeClaim</a>
array</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>volumeClaimTemplates 是 Pod 允许引用的 claim 列表。
StatefulSet controller 负责以保持 Pod 身份的方式将网络身份映射到 claim。
该列表中的每个 claim 都必须在模板中的某个容器里至少有一个同名的
volumeMount 与之匹配。对于同名项，此列表中的 claim 优先于模板中的任意
volume。</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p><strong><code>volumes</code></strong>
<em><a
href="https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.23/#volume-v1-core">Volume</a>
array</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>可由该 Pod 所属容器挂载的 volume 列表。
更多信息：<a
href="https://kubernetes.io/docs/concepts/storage/volumes">https://kubernetes.io/docs/concepts/storage/volumes</a></p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p><strong><code>resources</code></strong>
<em><a
href="https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.23/#resourcerequirements-v1-core">ResourceRequirements</a></em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>sidecar 的资源配置；如果设置，initcontainer 也会使用该配置。</p></td>
</tr>
</tbody>
</table>

## Tenant

Tenant 是一个 [Kubernetes
object](https://kubernetes.io/docs/concepts/overview/working-with-objects/kubernetes-objects/)
对象，用于描述 MinIO 租户。  

-   [TenantList](#tenantlist)

<table>
<colgroup>
<col style="width: 25%" />
<col style="width: 75%" />
</colgroup>
<thead>
<tr class="header">
<th style="text-align: left;">字段</th>
<th style="text-align: left;">说明</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td
style="text-align: left;"><p><strong><code>apiVersion</code></strong>
<em>string</em></p></td>
<td style="text-align: left;"><p><code>Operator CRD v2 Reference</code></p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p><strong><code>kind</code></strong>
<em>string</em></p></td>
<td style="text-align: left;"><p><code>Tenant</code></p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p><strong><code>metadata</code></strong>
<em><a
href="https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.23/#objectmeta-v1-meta">ObjectMeta</a></em></p></td>
<td style="text-align: left;"><p><code>metadata</code> 字段请参阅 Kubernetes API 文档。</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p><strong><code>scheduler</code></strong>
<em><a
href="#tenantscheduler">TenantScheduler</a></em></p></td>
<td style="text-align: left;"></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p><strong><code>spec</code></strong>
<em><a
href="#tenantspec">TenantSpec</a></em></p></td>
<td style="text-align: left;"><p><strong>必填</strong><br />
</p>
<p>MinIO 租户对象的根字段。</p></td>
</tr>
</tbody>
</table>

## TenantDomains

TenantDomains (`domains`) - 用于从 Kubernetes 集群外部访问租户的域名列表。
这只会为列出的域名配置 MinIO，但仍需要额外的外部 DNS 配置。
如果使用了 schema 和端口，列出的域名应包含它们，例如
<https://minio.domain.com:8123>

-   [Features](#features)

<table>
<colgroup>
<col style="width: 25%" />
<col style="width: 75%" />
</colgroup>
<thead>
<tr class="header">
<th style="text-align: left;">字段</th>
<th style="text-align: left;">说明</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;"><p><strong><code>minio</code></strong>
<em>string array</em></p></td>
<td style="text-align: left;"><p>MinIO 使用的域名列表。
这将启用对象存储的 DNS 风格访问方式，
其中存储桶名称会从域名中的子域推导出来。</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p><strong><code>console</code></strong>
<em>string</em></p></td>
<td style="text-align: left;"><p>用于暴露 MinIO Console 的域名。
这会在通过浏览器访问时为 MinIO 配置重定向。
如果 Console 通过子路径暴露，域名中也应包含该子路径，例如 <a
href="https://console.domain.com:8123/subpath/">https://console.domain.com:8123/subpath/</a></p></td>
</tr>
</tbody>
</table>

## TenantScheduler

TenantScheduler (`scheduler`) - 描述用于部署 MinIO 租户的 Kubernetes Scheduler 的对象。

-   [Tenant](#tenant)

<table>
<colgroup>
<col style="width: 25%" />
<col style="width: 75%" />
</colgroup>
<thead>
<tr class="header">
<th style="text-align: left;">字段</th>
<th style="text-align: left;">说明</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;"><p><strong><code>name</code></strong>
<em>string</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>指定用于调度 Tenant Pod 的 <a
href="https://kubernetes.io/docs/concepts/scheduling-eviction/kube-scheduler/">Kubernetes
scheduler</a> 名称。</p></td>
</tr>
</tbody>
</table>

## TenantSpec

TenantSpec (`spec`) 定义 MinIO 租户对象的配置。  

以下参数特指作为 MinIO Operator v4.0.0 一部分引入的
`Operator CRD v2 Reference` MinIO CRD API `spec` 定义。  

有关该对象的更完整文档，请参阅
[MinIO Kubernetes Documentation](https://silo.pigsty.cc/operations/deployments/kubernetes.html)。  

-   [Tenant](#tenant)

<table>
<colgroup>
<col style="width: 25%" />
<col style="width: 75%" />
</colgroup>
<thead>
<tr class="header">
<th style="text-align: left;">字段</th>
<th style="text-align: left;">说明</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;"><p><strong><code>pools</code></strong>
<em><a
href="#pool">Pool</a>
array</em></p></td>
<td style="text-align: left;"><p><strong>必填</strong><br />
</p>
<p>描述部署在 MinIO 租户中的各个 MinIO server pool 的对象数组。
每个 pool 由一组 MinIO server Pod 组成，这些 Pod 通过汇聚其存储资源来支持对象存储和检索请求。
每个 server pool 都独立于其他 pool，并支持在 MinIO 租户中横向扩展可用存储资源。<br />
</p>
<p>MinIO 租户 <code>spec</code> 的 <code>pools</code> 数组
<strong>必须</strong> 至少包含 <strong>一个</strong> 元素。<br />
</p>
<p>有关 <code>pools</code> 对象的示例和更完整文档，
请参阅 <a
href="https://silo.pigsty.cc/reference/operator-crd.html#tenant">MinIO
Operator CRD</a> 参考。</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p><strong><code>image</code></strong>
<em>string</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p></td>
</tr>
<tr class="odd">
<td
style="text-align: left;"><p><strong><code>imagePullSecret</code></strong>
<em><a
href="https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.23/#localobjectreference-v1-core">LocalObjectReference</a></em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>指定从私有 Docker 仓库拉取镜像时使用的 Secret。<br />
</p></td>
</tr>
<tr class="even">
<td
style="text-align: left;"><p><strong><code>podManagementPolicy</code></strong>
<em><a
href="https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.23/#podmanagementpolicytype-v1-apps">PodManagementPolicyType</a></em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>StatefulSet 创建 Pod 时使用的 Pod 管理策略。</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p><strong><code>env</code></strong>
<em><a
href="https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.23/#envvar-v1-core">EnvVar</a>
array</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>如果提供，MinIO Operator 在部署租户资源时会添加指定的环境变量。</p></td>
</tr>
<tr class="even">
<td
style="text-align: left;"><p><strong><code>externalCertSecret</code></strong>
<em><a
href="#localcertificatereference">LocalCertificateReference</a>
array</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>为租户中的每个 MinIO Pod 启用带 SNI 支持的 TLS。
如果省略
<code>externalCertSecret</code> <strong>且</strong>
<code>requestAutoCert</code> 设置为 <code>false</code>，
则 MinIO 租户会在 <strong>未启用</strong> TLS 的情况下部署。<br />
</p>
<p>指定一个 <a
href="https://kubernetes.io/docs/concepts/configuration/secret/">Kubernetes
TLS secret</a> 数组。MinIO Operator 会将指定证书复制到租户中的每个 MinIO
server Pod。当 MinIO Pod/Service 响应 TLS 连接请求时，会使用 SNI 选择
<code>subjectAlternativeName</code> 匹配的证书。<br />
</p>
<p><code>externalCertSecret</code> 数组中的每个元素都是一个包含以下字段的对象：<br />
</p>
<ul>
<li><p>- <code>name</code> - 包含 TLS 证书的 Kubernetes secret 名称。<br />
</p></li>
<li><p>- <code>type</code> - 指定
<code>kubernetes.io/tls</code><br />
</p></li>
</ul>
<p>有关为 MinIO 租户配置 TLS 的示例和更完整文档，
请参阅 <a
href="https://silo.pigsty.cc/reference/operator-crd.html#tenantspec">MinIO
Operator CRD</a> 参考。</p></td>
</tr>
<tr class="odd">
<td
style="text-align: left;"><p><strong><code>externalCaCertSecret</code></strong>
<em><a
href="#localcertificatereference">LocalCertificateReference</a>
array</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>允许 MinIO server Pod 验证由 Pod 信任存储之外的证书颁发机构签发的客户端 TLS 证书。<br />
</p>
<p>指定一个 <a
href="https://kubernetes.io/docs/concepts/configuration/secret/">Kubernetes
TLS secret</a> 数组。MinIO Operator 会将指定证书复制到租户中的每个 MinIO
server Pod。<br />
</p>
<p><code>externalCertSecret</code> 数组中的每个元素都是一个包含以下字段的对象：<br />
</p>
<ul>
<li><p>- <code>name</code> - 包含证书颁发机构的 Kubernetes secret 名称。<br />
</p></li>
<li><p>- <code>type</code> - 指定
<code>kubernetes.io/tls</code>.<br />
</p></li>
</ul>
<p>有关为 MinIO 租户配置 TLS 的示例和更完整文档，
请参阅 <a
href="https://silo.pigsty.cc/reference/operator-crd.html#tenantspec">MinIO
Operator CRD</a> 参考。</p></td>
</tr>
<tr class="even">
<td
style="text-align: left;"><p><strong><code>externalClientCertSecret</code></strong>
<em><a
href="#localcertificatereference">LocalCertificateReference</a></em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>在 MinIO 租户 Pod 与 <a
href="https://github.com/minio/kes">MinIO KES</a> 之间启用 mTLS 认证。
要启用 MinIO 租户与 MinIO KES 之间的连通性，此项为 <strong>必填</strong>。<br />
</p>
<p>指定一个 <a
href="https://kubernetes.io/docs/concepts/configuration/secret/">Kubernetes
TLS secret</a>。MinIO Operator 会将指定证书复制到租户中的每个 MinIO
server Pod。该 secret <strong>必须</strong> 包含以下字段：<br />
</p>
<ul>
<li><p><code>name</code> - 包含 TLS 证书的 Kubernetes secret 名称。<br />
</p></li>
<li><p><code>type</code> - 指定 <code>kubernetes.io/tls</code><br />
</p></li>
</ul>
<p>指定的证书 <strong>必须</strong> 对应 KES server 上的一个身份。
有关 KES 身份的更多信息，请参阅 <a
href="https://github.com/minio/kes/wiki/Configuration#policy-configuration">KES
Wiki</a>。<br />
</p>
<p>如果使用 MinIO Operator 部署 KES，请将该证书的哈希值作为
<a
href="#kesconfig"><code>kes</code></a>
对象规范的一部分。<br />
</p>
<p>有关为 MinIO 租户配置 TLS 的示例和更完整文档，
请参阅 <a
href="https://silo.pigsty.cc/reference/operator-crd.html#tenantspec">MinIO
Operator CRD</a> 参考。</p></td>
</tr>
<tr class="odd">
<td
style="text-align: left;"><p><strong><code>externalClientCertSecrets</code></strong>
<em><a
href="#localcertificatereference">LocalCertificateReference</a>
array</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>支持将额外客户端证书挂载到 MinIO 租户 Pod 中。
多个客户端证书将按以下目录结构挂载：<br />
</p>
<ul>
<li><p>certs<br />
</p></li>
<li><p>* client-0<br />
</p></li>
<li><p>* * client.crt<br />
</p></li>
<li><p>* * client.key<br />
</p></li>
<li><p>* client-1<br />
</p></li>
<li><p>* * client.crt<br />
</p></li>
<li><p>* * client.key<br />
</p></li>
<li><p>* * client-2<br />
</p></li>
<li><p>* client.crt<br />
</p></li>
<li><p>* * client.key<br />
</p></li>
</ul>
<p>指定一个 <a
href="https://kubernetes.io/docs/concepts/configuration/secret/">Kubernetes
TLS secret</a>。MinIO Operator 会将指定证书复制到租户中的每个 MinIO
server Pod，之后可通过环境变量引用。该 secret <strong>必须</strong>
包含以下字段：<br />
</p>
<ul>
<li><p><code>name</code> - 包含 TLS 证书的 Kubernetes secret 名称。<br />
</p></li>
<li><p><code>type</code> - 指定 <code>kubernetes.io/tls</code><br />
</p></li>
</ul></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p><strong><code>mountPath</code></strong>
<em>string</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>MinIO 卷（PV）的挂载路径。默认值为 <code>/export</code>。</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p><strong><code>subPath</code></strong>
<em>string</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>挂载路径内的子路径。这是 MinIO 存储数据的目录。
默认值为 <code>""</code>（空）。</p></td>
</tr>
<tr class="even">
<td
style="text-align: left;"><p><strong><code>requestAutoCert</code></strong>
<em>boolean</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>启用使用 <a
href="https://kubernetes.io/docs/tasks/tls/managing-tls-in-a-cluster/">基于 Kubernetes 的 TLS 证书生成</a>
与签发机制，为 MinIO 租户中的 Pod 和 Service 生成并签发证书。<br />
</p>
<ul>
<li><p>指定 <code>true</code> 以显式启用自动证书生成（默认）。<br />
</p></li>
<li><p>指定 <code>false</code> 以禁用自动证书生成。<br />
</p></li>
</ul>
<p>如果 <code>requestAutoCert</code> 设置为 <code>false</code>
<strong>且</strong> 省略了 <code>externalCertSecret</code>，
则 MinIO 租户会在 <strong>未启用</strong> TLS 的情况下部署。</p>
<p>有关为 MinIO 租户配置 TLS 的示例和更完整文档，
请参阅 <a
href="https://silo.pigsty.cc/reference/operator-crd.html#tenantspec">MinIO
Operator CRD</a> 参考。</p></td>
</tr>
<tr class="odd">
<td
style="text-align: left;"><p><strong><code>certExpiryAlertThreshold</code></strong>
<em>integer</em></p></td>
<td style="text-align: left;"><p>CertExpiryAlertThreshold 表示在触发证书即将过期告警前，最少需要剩余的天数。</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p><strong><code>liveness</code></strong>
<em><a
href="https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.23/#probe-v1-core">Probe</a></em></p></td>
<td style="text-align: left;"><p>用于容器存活性检查的 Liveness Probe。
如果探测失败，容器会重启。</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p><strong><code>readiness</code></strong>
<em><a
href="https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.23/#probe-v1-core">Probe</a></em></p></td>
<td style="text-align: left;"><p>用于容器就绪性检查的 Readiness Probe。
如果探测失败，容器会从服务端点中移除。</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p><strong><code>startup</code></strong>
<em><a
href="https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.23/#probe-v1-core">Probe</a></em></p></td>
<td style="text-align: left;"><p>Startup Probe 允许为 Pod 启动配置最长宽限期，
在此之前不会开始向其路由流量。</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p><strong><code>lifecycle</code></strong>
<em><a
href="https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.23/#lifecycle-v1-core">Lifecycle</a></em></p></td>
<td style="text-align: left;"><p>容器的 Lifecycle hook。</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p><strong><code>features</code></strong>
<em><a
href="#features">Features</a></em></p></td>
<td style="text-align: left;"><p>可启用或禁用与 S3 相关的功能，例如 <code>bucketDNS</code> 等。</p></td>
</tr>
<tr class="odd">
<td
style="text-align: left;"><p><strong><code>certConfig</code></strong>
<em><a
href="#certificateconfig">CertificateConfig</a></em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>启用为 Operator 自动生成的所有 TLS 证书设置
<code>CommonName</code>、<code>Organization</code> 和
<code>dnsName</code> 属性。
如果 <code>requestAutoCert</code> 为 <code>false</code>，
则配置该对象不会生效。<br />
</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p><strong><code>kes</code></strong>
<em><a
href="#kesconfig">KESConfig</a></em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>指示 MinIO Operator 使用指定配置部署 <a
href="https://github.com/minio/kes">MinIO Key Encryption Service</a>
(KES)。
MinIO KES 支持在 MinIO 租户上对对象执行服务端加密。<br />
</p></td>
</tr>
<tr class="odd">
<td
style="text-align: left;"><p><strong><code>prometheusOperator</code></strong>
<em>boolean</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>指示 MinIO Operator 使用 Prometheus Operator。<br />
</p>
<p>租户的 scrape 配置会添加到由 prometheus-operator 管理的 Prometheus 中。</p></td>
</tr>
<tr class="even">
<td
style="text-align: left;"><p><strong><code>serviceAccountName</code></strong>
<em>string</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>用于运行作为租户一部分创建的 MinIO Pod 的
<a
href="https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/">Kubernetes
Service Account</a>。<br />
</p></td>
</tr>
<tr class="odd">
<td
style="text-align: left;"><p><strong><code>priorityClassName</code></strong>
<em>string</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>指示 Pod 的优先级，即其相对于集群中其他 Pod 的重要程度。
这仅应用于 MinIO Pod。<br />
</p>
<p>更完整的文档请参阅 Kubernetes 的 <a
href="https://kubernetes.io/docs/concepts/configuration/pod-priority-preemption/#priorityclass">Priority
Class 文档</a>。</p></td>
</tr>
<tr class="even">
<td
style="text-align: left;"><p><strong><code>imagePullPolicy</code></strong>
<em><a
href="https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.23/#pullpolicy-v1-core">PullPolicy</a></em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>MinIO Docker 镜像的拉取策略。可指定以下值：<br />
</p>
<ul>
<li><p><code>Always</code><br />
</p></li>
<li><p><code>Never</code><br />
</p></li>
<li><p><code>IfNotPresent</code>（默认）<br />
</p></li>
</ul>
<p>详细信息请参阅 Kubernetes 文档：<a
href="https://kubernetes.io/docs/concepts/containers/images#updating-images">https://kubernetes.io/docs/concepts/containers/images#updating-images</a></p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p><strong><code>sideCars</code></strong>
<em><a
href="#sidecars">SideCars</a></em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>作为 sidecar 随租户中每个 MinIO Pod 一同运行的容器列表。</p></td>
</tr>
<tr class="even">
<td
style="text-align: left;"><p><strong><code>exposeServices</code></strong>
<em><a
href="#exposeservices">ExposeServices</a></em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>指示 Operator 暴露 MinIO 和/或 Console 服务。<br />
</p></td>
</tr>
<tr class="odd">
<td
style="text-align: left;"><p><strong><code>serviceMetadata</code></strong>
<em><a
href="#servicemetadata">ServiceMetadata</a></em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>指定要追加到 MinIO 服务和/或 Console 服务的自定义 labels 和 annotations。</p></td>
</tr>
<tr class="even">
<td
style="text-align: left;"><p><strong><code>poolsMetadata</code></strong>
<em><a
href="#poolsmetadata">PoolsMetadata</a></em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>指定要追加到所有 pool StatefulSet 和 Pod 的自定义 labels 和 annotations。</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p><strong><code>users</code></strong>
<em><a
href="https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.23/#localobjectreference-v1-core">LocalObjectReference</a>
array</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>一个用于在租户置备期间生成 MinIO 用户的 <a
href="https://kubernetes.io/docs/concepts/configuration/secret/">Kubernetes
opaque secret</a> 数组。<br />
</p>
<p>数组中的每个元素都是一个由键值对
<code>name: &lt;string&gt;</code> 组成的对象，
其中 <code>&lt;string&gt;</code> 引用一个 Kubernetes opaque secret。<br />
</p>
<p>每个被引用的 Kubernetes secret 都必须包含以下字段：<br />
</p>
<ul>
<li><p><code>CONSOLE_ACCESS_KEY</code> - MinIO 用户的“用户名”<br />
</p></li>
<li><p><code>CONSOLE_SECRET_KEY</code> - MinIO 用户的“密码”<br />
</p></li>
</ul>
<p>Operator 默认会为每个用户分配 <code>consoleAdmin</code> policy。
Tenant 启动后，你可以修改分配给它的 policy。<br />
</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p><strong><code>buckets</code></strong>
<em><a
href="#bucket">Bucket</a>
array</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>在创建新租户时创建存储桶。
如果给定名称的存储桶已存在，则跳过。</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p><strong><code>logging</code></strong>
<em><a
href="#logging">Logging</a></em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>为 MinIO 租户启用 JSON 和 Anonymous 日志。</p></td>
</tr>
<tr class="even">
<td
style="text-align: left;"><p><strong><code>configuration</code></strong>
<em><a
href="https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.23/#localobjectreference-v1-core">LocalObjectReference</a></em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>指定一个包含额外环境变量配置的 Secret，供 MinIO pool 使用。
该 Secret 应包含一个名为 <code>config.env</code> 的键，
其中包含 MinIO+ 的所有导出环境变量。</p></td>
</tr>
<tr class="odd">
<td
style="text-align: left;"><p><strong><code>initContainers</code></strong>
<em><a
href="https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.23/#container-v1-core">Container</a>
array</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>向 StatefulSet 添加自定义 initContainer。</p></td>
</tr>
<tr class="even">
<td
style="text-align: left;"><p><strong><code>additionalVolumes</code></strong>
<em><a
href="https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.23/#volume-v1-core">Volume</a>
array</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>如果提供，StatefulSet 将附加这些 volume。
你应为对应的 volume 和 volumeMount 设置规则。
Operator 不会验证这些规则，最终结果以 Kubernetes 为准。</p></td>
</tr>
<tr class="odd">
<td
style="text-align: left;"><p><strong><code>additionalVolumeMounts</code></strong>
<em><a
href="https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.23/#volumemount-v1-core">VolumeMount</a>
array</em></p></td>
<td style="text-align: left;"><p><strong>可选</strong><br />
</p>
<p>如果提供，StatefulSet 将附加这些 volume mount。
你应为对应的 volume 和 volumeMount 设置规则。
Operator 不会验证这些规则，最终结果以 Kubernetes 为准。</p></td>
</tr>
</tbody>
</table>

## TenantUsage

TenantUsage 是关于租户使用量和容量的指标。

-   [TenantStatus](#tenantstatus)

<table>
<colgroup>
<col style="width: 25%" />
<col style="width: 75%" />
</colgroup>
<thead>
<tr class="header">
<th style="text-align: left;">字段</th>
<th style="text-align: left;">说明</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;"><p><strong><code>capacity</code></strong>
<em>integer</em></p></td>
<td style="text-align: left;"><p>该租户的可用容量，单位为字节。</p></td>
</tr>
<tr class="even">
<td
style="text-align: left;"><p><strong><code>rawCapacity</code></strong>
<em>integer</em></p></td>
<td style="text-align: left;"><p>该租户的原始容量，单位为字节。</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p><strong><code>usage</code></strong>
<em>integer</em></p></td>
<td style="text-align: left;"><p>由 MinIO 管理的数据量，单位为字节。</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p><strong><code>rawUsage</code></strong>
<em>integer</em></p></td>
<td style="text-align: left;"><p>磁盘上的原始使用量，单位为字节。</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p><strong><code>tiers</code></strong>
<em><a
href="#tierusage">TierUsage</a>
array</em></p></td>
<td style="text-align: left;"><p>Tiers 包含租户中各个 tier 的使用情况。</p></td>
</tr>
</tbody>
</table>

## TierUsage

TierUsage 表示租户配置的某个 tier 的使用情况。

-   [TenantUsage](#tenantusage)

<table>
<colgroup>
<col style="width: 25%" />
<col style="width: 75%" />
</colgroup>
<thead>
<tr class="header">
<th style="text-align: left;">字段</th>
<th style="text-align: left;">说明</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td style="text-align: left;"><p><strong><code>Name</code></strong>
<em>string</em></p></td>
<td style="text-align: left;"><p>tier 名称。</p></td>
</tr>
<tr class="even">
<td style="text-align: left;"><p><strong><code>Type</code></strong>
<em>string</em></p></td>
<td style="text-align: left;"><p>tier 类型。</p></td>
</tr>
<tr class="odd">
<td style="text-align: left;"><p><strong><code>totalSize</code></strong>
<em>integer</em></p></td>
<td style="text-align: left;"><p>该 tier 的总使用量。</p></td>
</tr>
</tbody>
</table>
