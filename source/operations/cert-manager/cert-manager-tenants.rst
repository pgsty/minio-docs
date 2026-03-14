.. _minio-certmanager-tenants:

========================================
租户的 cert-manager
========================================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 1

以下过程用于创建并应用在租户内使用 cert-manager 管理 TLS 证书所需的资源。

.. note::

   本过程使用 ``tenant-1`` 作为租户名称。
   
   请在整个过程中将 ``tenant-1`` 替换为你的租户名称。

前提条件
--------

- 已安装 `kustomize <https://kustomize.io/>`__
- 可通过 ``kubectl`` 访问你的 ``k8s`` 集群
- 已完成 :ref:`cert-manager 的设置 <minio-setup-certmanager>`
- 已安装 MinIO Operator，并已 :ref:`完成 cert-manager 设置 <minio-certmanager-operator>`

1) 创建租户命名空间的 CA Issuer
-------------------------------

在部署新租户之前，先为该租户的命名空间创建 Certificate Authority 和 ``Issuer``。

1. 如有必要，先创建租户命名空间。

   .. code-block:: shell
      :class: copyable

      kubectl create ns tenant-1

   该值必须与租户 YAML 中 ``metadata.namespace`` 字段的值一致。

2. 为新的 Certificate Authority 申请一个 ``Certificate``，并将 ``spec.isCA`` 设为 ``true``。

   创建一个名为 ``tenant-1-ca-certificate.yaml`` 的文件，内容如下：

   .. code-block:: yaml
      :class: copyable
      :emphasize-lines: 7,8

      # tenant-1-ca-certificate.yaml
      apiVersion: cert-manager.io/v1
      kind: Certificate
      metadata:
        name: tenant-1-ca-certificate
        namespace: tenant-1
      spec:
        isCA: true
        commonName: tenant-1-ca
        secretName: tenant-1-ca-tls
        duration: 70128h # 8y
        privateKey:
          algorithm: ECDSA
          size: 256
        issuerRef:
          name: selfsigned-root
          kind: ClusterIssuer
          group: cert-manager.io

   .. important::

      ``spec.issueRef.name`` 必须与 :ref:`设置 cert-manager <minio-cert-manager-create-cluster-issuer>` 时创建的 ``ClusterIssuer`` 名称一致。
      如果你使用了不同的 ``ClusterIssuer`` 名称，或者采用了与本指南不同的 ``Issuer``，请按你的环境修改 ``issuerRef``。


3. 应用该资源：

   .. code-block:: shell
      :class: copyable

      kubectl apply -f tenant-1-ca-certificate.yaml

2) 创建 ``Issuer``
------------------

``Issuer`` 负责在租户命名空间内签发证书。

1. 生成一个 ``Issuer`` 的资源定义。

   创建一个名为 ``tenant-1-ca-issuer.yaml`` 的文件，内容如下：

   .. code-block:: yaml
      :class: copyable

      # tenant-1-ca-issuer.yaml
      apiVersion: cert-manager.io/v1
      kind: Issuer
      metadata:
        name: tenant-1-ca-issuer
        namespace: tenant-1
      spec:
        ca:
          secretName: tenant-1-ca-tls

2. 应用该 ``Issuer`` 资源定义：

   .. code-block:: shell
      :class: copyable

      kubectl apply -f tenant-1-ca-issuer.yaml

3) 为租户创建证书
-----------------

请求 cert-manager 为 MinIO 签发新的 TLS server 证书。
该证书必须对以下 DNS 域名有效：

- ``minio.<namespace>``
- ``minio.<namespace>.svc``
- ``minio.<namespace>.svc.<cluster domain>``
- ``*.<tenant-name>-hl.<namespace>.svc.<cluster domain>``
- ``*.<namespace>.svc.<cluster domain>``
- ``*.<tenant-name>.minio.<namespace>.svc.<cluster domain>'``

.. important::

   请将占位符文本（由 ``<`` 和 ``>`` 标识）替换为你的租户实际值：

   - ``<cluster domain>`` 是 Kubernetes 集群内部的根 DNS 域。
     通常为 ``cluster.local``，但你仍应检查 CoreDNS 配置，以确认 Kubernetes 集群实际使用的值。
      
     例如：

     .. code-block:: shell
        :class: copyable

        kubectl get configmap coredns -n kube-system -o jsonpath="{.data}"

     不同 Kubernetes 提供方对根域名的管理方式各不相同。
     更多信息请参考你的 Kubernetes 提供方文档。

   - ``tenant-name`` 是 Tenant YAML 中 ``metadata.name`` 字段指定的名称。
     在本例中该值为 ``myminio``。

   - ``namespace`` 是前面创建、将用于安装租户的命名空间值。
     在 Tenant YAML 中，它定义在 ``metadata.namespace`` 字段。
     在本例中该值为 ``tenant-1``。

1. 为指定域名申请一个 ``Certificate``

   创建一个名为 ``tenant-1-minio-certificate.yaml`` 的文件。
   文件内容应类似如下，并根据你的集群和租户配置进行调整：

   .. code-block:: yaml
      :class: copyable

      # tenant-1-minio-certificate.yaml
      apiVersion: cert-manager.io/v1
      kind: Certificate
      metadata:
        name: tenant-certmanager-cert
        namespace: tenant-1
      spec:
        dnsNames:
          - "minio.tenant-1"
          - "minio.tenant-1.svc"
          - 'minio.tenant-1.svc.cluster.local'
          - '*.minio.tenant-1.svc.cluster.local'
          - '*.myminio-hl.tenant-1.svc.cluster.local'
          - '*.myminio.minio.tenant-1.svc.cluster.local'
        secretName: myminio-tls
        issuerRef:
          name: tenant-1-ca-issuer

   .. tip::

      在本例中，Tenant 名称为 ``myminio``。
      作为命名约定，建议将 ``spec.secretName`` 字段中的 secret 命名为 ``<tenant-name>-tls``。

2. 应用该证书资源：

   .. code-block:: shell
      :class: copyable

      kubectl apply -f tenant-1-minio-certificate.yaml

3. 验证变更是否已生效：

   .. code-block:: shell
      :class: copyable

      kubectl describe secret/myminio-tls -n tenant-1

   .. note::

      - 将 ``tenant-1`` 替换为你的租户命名空间。
      - 如果 secret 名称不同，请将 ``myminio-tls`` 替换为实际值。

4) 使用 cert-manager 部署租户并管理 TLS 证书
------------------------------------------------------------

部署 Tenant 时，必须将 TLS 配置设置为以下状态：

- Tenant 不会自动生成自己的证书（``spec.requestAutoCert: false``）*并且*
- Tenant 具有有效的 cert-manager 引用（``spec.externalCertSecret``）

这会指示 Operator 在部署 Tenant 时仅使用 cert-manager 管理的证书。

以下 YAML ``spec`` 提供了满足这些要求的基线配置：

.. code-block:: yaml
   :emphasize-lines: 6,9,11

   apiVersion: minio.min.io/v2
   kind: Tenant
   metadata:
     name: myminio
     namespace: tenant-1
   spec:
   ...
     ## Disable default tls certificates.
     requestAutoCert: false
     ## Use certificates generated by cert-manager.
     externalCertSecret:
       - name: myminio-tls
         type: cert-manager.io/v1
   ...

5) 在 MinIO Operator 中信任租户 CA
------------------------------------------------

默认情况下，MinIO Operator 不信任租户的 CA。
若要让 Operator 信任该 CA，你必须将该证书作为 secret 传递给 Operator。

为此，请在 ``minio-operator`` 命名空间中创建一个以 ``operator-ca-tls-`` 为前缀、后接唯一标识符命名的 secret。

MinIO Operator 会挂载并信任由所提供的 Certificate Authority 签发的 **所有** 证书。
这是必需的，因为 MinIO Operator 会通过 ``/minio/health/cluster`` 端点执行健康检查。

创建 ``operator-ca-tls-tenant-1`` secret
+++++++++++++++++++++++++++++++++++++++++++++++

将租户中由 cert-manager 生成的 CA 公钥（``ca.crt``）复制到 ``minio-operator`` 命名空间中。
这样 Operator 就能信任该 cert-manager 颁发的 CA 及其派生的所有证书。

1. 创建一个包含该 CA 的 ``ca.crt`` 文件：

   .. code-block:: shell
      :class: copyable

      kubectl get secrets -n tenant-1 tenant-1-ca-tls -o=jsonpath='{.data.ca\.crt}' | base64 -d > ca.crt

2. 创建 secret：

   .. code-block:: shell
      :class: copyable

      kubectl create secret generic operator-ca-tls-tenant-1 --from-file=ca.crt -n minio-operator

.. tip::

   在本例中，我们选择将 secret 命名为 ``operator-ca-tls-tenant-1``。
   我们使用租户命名空间 ``tenant-1`` 作为后缀，以便更容易识别该 CA 来自哪个命名空间。
   建议使用你的租户命名空间名称作为后缀，以便将 secret 与相关资源更容易关联起来。

6) 部署租户
-----------

租户命名空间中的 Certificate Authority 和 ``Issuer`` 就绪后，你现在可以 :ref:`部署对象存储租户 <minio-k8s-deploy-minio-tenant>`。

请使用修改后的基线 Tenant YAML，禁用 AutoCert 并引用刚刚生成的 secret。
