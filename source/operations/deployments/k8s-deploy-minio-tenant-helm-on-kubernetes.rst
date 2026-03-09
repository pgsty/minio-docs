.. _deploy-tenant-helm:

======================================
使用 Helm Charts 部署 MinIO Tenant
======================================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 1

概览
----

Helm 是一个用于将应用自动部署到 Kubernetes 集群的工具。
`Helm chart <https://helm.sh/docs/topics/charts/>`__ 是一组定义部署细节的 YAML 文件、模板和其他文件。
以下步骤使用 Helm Chart 部署由 MinIO Operator 管理的 Tenant。

本步骤要求 Kubernetes 集群中已存在一个有效的 :ref:`Operator <deploy-operator-kubernetes>` 部署。
你不能使用 MinIO Operator Tenant chart 在脱离 Operator 的情况下独立部署 Tenant。

.. important::

   MinIO Operator Tenant Chart 与社区维护的 :minio-git:`MinIO Chart <minio/tree/master/helm/minio>` *不同*。

   社区 Helm Chart 由社区构建、维护并提供支持。
   对于引用该 chart 的任何 bug、功能请求或更新，MinIO 均不保证提供支持。

   :ref:`Operator Tenant Chart <minio-tenant-chart-values>` 由 MinIO 官方维护并提供支持。
   对于生产环境，MinIO 强烈建议为 :ref:`Operator <minio-operator-chart-values>` 和 :ref:`Tenant <minio-tenant-chart-values>` 使用官方 Helm Chart。

前提条件
--------

要使用 Helm 安装 MinIO Tenant，你必须满足以下要求：

- 一个现有的 Kubernetes 集群
- 本地主机上安装了与集群版本匹配的 ``kubectl`` CLI 工具
- `Helm <https://helm.sh/docs/intro/install/>`__ 3.8 或更高版本
- `yq <https://github.com/mikefarah/yq/#install>`__ 4.18.1 或更高版本
- 一个现有的 :ref:`MinIO Operator 安装 <deploy-operator-kubernetes>`

本步骤默认你对 Kubernetes 集群的访问权限具备较广泛的管理能力。

有关 Tenant 安装要求的更多信息，包括受支持的 Kubernetes 版本和 TLS 证书，请参阅 :ref:`Tenant 部署前提条件 <minio-hardware-checklist-storage>`。

本步骤默认你已经熟悉相关 Kubernetes 概念和工具。
虽然本文档可能会以 best-effort 方式提供 Kubernetes 相关资源的配置或部署指导，但它不能替代官方 :kube-docs:`Kubernetes Documentation <>`。

命名空间
~~~~~~~~

租户必须使用自己的命名空间，不能与其他租户共享命名空间。
此外，MinIO 强烈建议为租户使用专用命名空间，且该命名空间内不要运行其他应用。

.. _deploy-tenant-helm-repo:

使用 Helm Charts 部署 MinIO Tenant
------------------------------------

以下步骤使用 MinIO Operator Chart Repository 部署 MinIO Tenant。
与 :ref:`本地 chart 安装 <deploy-tenant-helm-local>` 相比，这种方式的安装路径更简单。


以下步骤使用 Helm 通过官方 MinIO Tenant Chart 部署 MinIO Tenant。

.. important::

   如果你使用 Helm 部署 MinIO Tenant，就必须使用 Helm 来管理或升级该部署。
   不要使用 ``kubectl krew``、Kustomize 或类似方式来管理或升级 MinIO Tenant。

本步骤并未穷尽 :ref:`Tenant Chart <minio-tenant-chart-values>` 中所有可能的配置项。
它只提供一个基线，你可以在此基础上按需修改和定制 Tenant。

.. container:: procedure

   #. 验证 MinIO Operator Repo 配置

      MinIO 在 https://operator.min.io 维护了一个兼容 Helm 的仓库。
      如果该仓库尚未存在于本地 Helm 配置中，请先添加后再继续：

      .. code-block:: shell
         :class: copyable

         helm repo add minio-operator https://operator.min.io

      你可以使用 ``helm search`` 验证仓库内容：

      .. code-block:: shell
         :class: copyable

         helm search repo minio-operator

      返回结果应类似如下：

      .. code-block:: shell
         :class: copyable
         :substitutions:

         NAME                            CHART VERSION   APP VERSION     DESCRIPTION                    
         minio-operator/minio-operator   4.3.7           v4.3.7          A Helm chart for MinIO Operator
         minio-operator/operator         |operator-version-stable|           v|operator-version-stable|          A Helm chart for MinIO Operator
         minio-operator/tenant           |operator-version-stable|           v|operator-version-stable|          A Helm chart for MinIO Operator

   #. 创建 Helm ``values.yaml`` 的本地副本以供修改

      .. code-block:: shell
         :class: copyable

         curl -sLo values.yaml https://raw.githubusercontent.com/minio/operator/master/helm/tenant/values.yaml

      请使用你偏好的文本编辑器打开 ``values.yaml`` 文件。

   #. 配置 Tenant 拓扑
      
      以下字段都带有 ``tenant.pools[0]`` 前缀，用于控制 Tenant 中所有 pod 的 server 数量、每个 server 的卷数量以及存储类：
      
      .. list-table::
         :header-rows: 1
         :widths: 30 70

         * - 字段
           - 描述

         * - ``servers`` 
           - 要在 Server Pool 中部署的 MinIO pod 数量。
         
         * - ``volumesPerServer`` 
           - 每个 MinIO pod（``servers``）要挂载的持久卷数量。
             Operator 会为该 Tenant 生成 ``volumesPerServer x servers`` 个 Persistent Volume Claim。
         
         * - ``storageClassName`` 
           - 与生成的 Persistent Volume Claim 关联的 Kubernetes 存储类。

             如果不存在与指定值匹配的存储类，*或者* 指定的存储类无法满足所请求的 PVC 数量或存储容量，Tenant 可能无法启动。

         * - ``size``
           - 为每个生成的 PVC 请求的存储容量。

   #. 配置 Tenant Affinity 或 Anti-Affinity

      Tenant Chart 支持以下 Kubernetes Selector、Affinity 和 Anti-Affinity 配置：

      - Node Selector (``tenant.nodeSelector``)
      - Node/Pod Affinity or Anti-Affinity (``spec.pools[n].affinity``)

      MinIO 建议为 Tenant 配置 Pod Anti-Affinity，以确保 Kubernetes 调度器不会将多个 pod 调度到同一个 worker node 上。

      如果你希望将租户部署到特定 worker node 上，请将对应的 node label 或过滤条件传入 ``nodeSelector`` 或 ``affinity`` 字段，以约束调度器仅在这些节点上放置 pod。

   #. 配置网络加密

      MinIO Tenant CRD 提供了以下字段，你可以通过它们配置租户的 TLS 网络加密：

      .. list-table::
         :header-rows: 1
         :widths: 30 70

         * - 字段
           - 描述

         * - ``tenant.certificate.requestAutoCert``
           - 启用或禁用 MinIO :ref:`自动 TLS 证书生成 <minio-tls>`。

             若省略该字段，默认值为 ``true``，即启用。

         * - ``tenant.certificate.certConfig``
           - 在启用的情况下，自定义 :ref:`自动 TLS <minio-tls>` 的行为。

         * - ``tenant.certificate.externalCertSecret``
           - 通过 Server Name Indication (SNI) 为多个主机名启用 TLS。
         
             指定一个或多个类型为 ``kubernetes.io/tls`` 或 ``cert-manager`` 的 Kubernetes secret。

         * - ``tenant.certificate.externalCACertSecret``
           - 启用对由未知、第三方或内部 Certificate Authorities (CA) 签发的客户端 TLS 证书的校验。
         
             指定一个或多个类型为 ``kubernetes.io/tls`` 的 Kubernetes secret，其中包含某个 CA 的完整证书链。

   #. 配置 MinIO 环境变量

      你可以使用 ``tenant.configuration`` 字段设置 MinIO Server 环境变量。

      .. list-table::
         :header-rows: 1
         :widths: 30 70

         * - 字段
           - 描述

         * - ``tenant.configuration``
           - 指定一个 Kubernetes opaque secret，其数据负载 ``config.env`` 包含你希望设置的每一个 MinIO 环境变量。

             ``config.env`` 数据负载 **必须** 是一个 base64 编码字符串。
             你可以先创建一个本地文件，在其中设置环境变量，再通过 ``cat LOCALFILE | base64`` 生成该负载。

      该 YAML 中包含一个 ``kind: Secret`` 且 ``metadata.name: storage-configuration`` 的对象，用于设置 root 用户名、密码、纠删码校验设置，以及启用 Tenant Console。

      请根据 Tenant 的实际需求修改这些值。

   #. 部署 Tenant

      使用 ``helm`` 安装 Tenant Chart，并将 ``values.yaml`` 作为覆盖配置：

      .. code-block:: shell
         :class: copyable

         helm install \
         --namespace TENANT-NAMESPACE \
         --create-namespace \
         --values values.yaml \
         TENANT-NAME minio-operator/tenant

      你可以使用以下命令监控进度：

      .. code-block:: shell
         :class: copyable

         watch kubectl get all -n TENANT-NAMESPACE

   #. 暴露 Tenant 的 MinIO S3 API 端口

      若要在本地机器上测试 MinIO Client :mc:`mc`，请转发 MinIO 端口并创建别名。

      * 转发 Tenant 的 MinIO 端口：

      .. code-block:: shell
         :class: copyable

         kubectl port-forward svc/TENANT-NAME-hl 9000 -n TENANT-NAMESPACE

      * 为 Tenant 服务创建别名：

      .. code-block:: shell
         :class: copyable

         mc alias set myminio https://localhost:9000 minio minio123 --insecure

      你可以使用 :mc:`mc mb` 在 Tenant 上创建存储桶：
      
      .. code-block:: shell
         :class: copyable

         mc mb myminio/mybucket --insecure

      如果你为 MinIO Tenant 部署的是由受信任 Certificate Authority (CA) 签发的 TLS 证书，则可以省略 ``--insecure`` 参数。

      有关连接 Tenant 的更多文档，请参阅 :ref:`create-tenant-connect-tenant`。

.. _deploy-tenant-helm-local:

使用本地 Helm Chart 部署 Tenant
--------------------------------

以下步骤使用 Helm Charts 的本地副本部署 Tenant。
与 :ref:`基于仓库的安装 <deploy-tenant-helm-repo>` 相比，这种方式可能更便于在安装前完成 Tenant 预配置。

#. 下载 Helm charts

   在本地主机上，将 Tenant Helm charts 下载到一个合适的目录：

   .. code-block:: shell
      :class: copyable
      :substitutions:

      curl -O https://raw.githubusercontent.com/minio/operator/master/helm-releases/tenant-|operator-version-stable|.tgz

   每个 chart 都包含一个可按需定制的 ``values.yaml`` 文件。
   有关 MinIO Tenant ``values.yaml`` 可用选项的详细信息，请参阅 :ref:`minio-tenant-chart-values`。
   
   请使用你偏好的文本编辑器打开 ``values.yaml`` 文件。

#. 配置 Tenant 拓扑
   
   以下字段都带有 ``tenant.pools[0]`` 前缀，用于控制 Tenant 中所有 pod 的 server 数量、每个 server 的卷数量以及存储类：
   
   .. list-table::
      :header-rows: 1
      :widths: 30 70

      * - 字段
        - 描述

      * - ``servers`` 
        - 要在 Server Pool 中部署的 MinIO pod 数量。
      * - ``volumesPerServer`` 
        - 每个 MinIO pod（``servers``）要挂载的持久卷数量。
          Operator 会为该 Tenant 生成 ``volumesPerServer x servers`` 个 Persistent Volume Claim。
      * - ``storageClassName`` 
        - 与生成的 Persistent Volume Claim 关联的 Kubernetes 存储类。

          如果不存在与指定值匹配的存储类，*或者* 指定的存储类无法满足所请求的 PVC 数量或存储容量，Tenant 可能无法启动。

      * - ``size``
        - 为每个生成的 PVC 请求的存储容量。

#. 配置 Tenant Affinity 或 Anti-Affinity

   Tenant Chart 支持以下 Kubernetes Selector、Affinity 和 Anti-Affinity 配置：

   - Node Selector (``tenant.nodeSelector``)
   - Node/Pod Affinity or Anti-Affinity (``spec.pools[n].affinity``)

   MinIO 建议为 Tenant 配置 Pod Anti-Affinity，以确保 Kubernetes 调度器不会将多个 pod 调度到同一个 worker node 上。

   如果你希望将租户部署到特定 worker node 上，请将对应的 node label 或过滤条件传入 ``nodeSelector`` 或 ``affinity`` 字段，以约束调度器仅在这些节点上放置 pod。

#. 配置网络加密

   MinIO Tenant CRD 提供了以下字段，你可以通过它们配置租户的 TLS 网络加密：

   .. list-table::
      :header-rows: 1
      :widths: 30 70

      * - 字段
        - 描述

      * - ``tenant.certificate.requestAutoCert``
        - 启用或禁用 MinIO :ref:`自动 TLS 证书生成 <minio-tls>`

      * - ``tenant.certificate.certConfig``
        - 控制 :ref:`自动 TLS <minio-tls>` 的设置。
          需要 ``spec.requestAutoCert: true``

      * - ``tenant.certificate.externalCertSecret``
        - 指定一个或多个类型为 ``kubernetes.io/tls`` 或 ``cert-manager`` 的 Kubernetes secret。
          MinIO 会基于主机名（Server Name Indication）使用这些证书执行 TLS 握手。

      * - ``tenant.certificate.externalCACertSecret``
        - 指定一个或多个类型为 ``kubernetes.io/tls`` 的 Kubernetes secret，其中包含 Tenant 为允许客户端 TLS 连接而必须信任的 Certificate Authority (CA) 证书链。

#. 配置 MinIO 环境变量

   你可以使用 ``tenant.configuration`` 字段设置 MinIO Server 环境变量。

   该字段必须指定一个 Kubernetes opaque secret，其数据负载 ``config.env`` 包含你希望设置的每一个 MinIO 环境变量。

   该 YAML 中包含一个 ``kind: Secret`` 且 ``metadata.name: storage-configuration`` 的对象，用于设置 root 用户名、密码、纠删码校验设置，以及启用 Tenant Console。

   请根据 Tenant 的实际需求修改这些值。

#. 以下 Helm 命令会使用标准 chart 创建 MinIO Tenant：

   .. code-block:: shell
      :class: copyable
      :substitutions:

      helm install \
      --namespace TENANT-NAMESPACE \
      --create-namespace \
      TENANT-NAME tenant-|operator-version-stable|.tgz

   若要部署多个 Tenant，请创建一个包含新 Tenant 详细配置的 Helm chart，并重复上述部署步骤。
   重新部署同一个 chart 会更新此前已部署的 Tenant。

#. 暴露 Tenant 的 MinIO 端口

   若要在本地机器上测试 MinIO Client :mc:`mc`，请转发 MinIO 端口并创建别名。

   * 转发 Tenant 的 MinIO 端口：

     .. code-block:: shell
        :class: copyable

        kubectl port-forward svc/TENANT-NAME-hl 9000 -n TENANT-NAMESPACE

   * 为 Tenant 服务创建别名：

     .. code-block:: shell
        :class: copyable

        mc alias set myminio https://localhost:9000 minio minio123 --insecure

     该示例使用非 TLS 的 ``myminio-hl`` 服务，因此需要 ``--insecure`` 参数。

     如果你已经配置了 TLS 证书，请省略 ``--insecure``，并改用 ``svc/minio``。

   你可以使用 :mc:`mc mb` 在 Tenant 上创建存储桶：
   
     .. code-block:: shell
        :class: copyable

        mc mb myminio/mybucket --insecure

有关连接 Tenant 的更多文档，请参阅 :ref:`create-tenant-connect-tenant`。
