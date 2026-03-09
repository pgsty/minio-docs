.. _minio-k8s-deploy-minio-tenant:
.. _deploy-minio-tenant-redhat-openshift:

=========================
部署一个 MinIO Tenant
=========================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 1

本步骤说明如何使用 MinIO Operator 部署 MinIO Tenant。

.. screenshot temporarily removed

   .. image:: /images/k8s/operator-dashboard.png
   :align: center
   :width: 70%
   :class: no-scaled-link
   :alt: MinIO Operator Console


部署 Single-Node 拓扑需要额外配置，而这些内容不在本文档覆盖范围内。
如果只是用于本地测试或评估，你也可以根据需要使用简单的 Kubernetes YAML 对象来描述 Single-Node 拓扑。
MinIO 不建议，也不支持在生产环境中使用单节点部署拓扑。

本文档默认你已经熟悉所有被引用的 Kubernetes 概念、工具和操作流程。
虽然本文档 *可能* 会以 best-effort 方式提供 Kubernetes 相关资源的配置或部署指导，但它不能替代官方 :kube-docs:`Kubernetes Documentation <>`。

.. _minio-k8s-deploy-minio-tenant-security:

使用 Kustomize 部署 MinIO Tenant
----------------------------------

以下步骤使用 ``kubectl -k``，基于 :minio-git:`MinIO Operator Github 仓库 <operator/tree/master/examples/kustomization/base>` 中的 ``base`` Kustomization 模板来部署 MinIO Tenant。

你也可以从该 :minio-git:`仓库 <operator/tree/master/examples/kustomization/>` 选择其他 base 或预构建模板作为起点，或者依据 :ref:`MinIO Custom Resource Documentation <minio-operator-crd>` 自行构建 Kustomization 资源。

.. important::

   如果你使用 Kustomize 部署 MinIO Tenant，就必须使用 Kustomize 来管理或升级该部署。
   不要使用 ``kubectl krew``、Helm Chart 或类似方式来管理或升级该 MinIO Tenant。

本步骤并未穷尽 :ref:`Tenant CRD <minio-operator-crd>` 中的所有可配置项。
它只提供一个基线，你可以在此基础上按需修改和定制 Tenant。

.. container:: procedure

   #. 为 Tenant 创建 YAML 对象

      使用 ``kubectl kustomize`` 命令生成一个 YAML 文件，其中包含部署 ``base`` Tenant 所需的全部 Kubernetes 资源：

      .. code-block:: shell
         :class: copyable

         kubectl kustomize https://github.com/minio/operator/examples/kustomization/base/ > tenant-base.yaml

      该命令会创建一个单独的 YAML 文件，多个对象之间使用 ``---`` 分隔。
      请使用你偏好的编辑器打开此文件。

      下文各步骤将根据对象的 ``kind`` 和 ``metadata.name`` 字段来引用这些对象：

   #. 配置 Tenant 拓扑

      ``kind: Tenant`` 对象用于描述 MinIO Tenant。

      以下字段都带有 ``spec.pools[0]`` 前缀，用于控制 Tenant 中所有 pod 的 server 数量、每个 server 的卷数量以及存储类：
      
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

         * - ``volumeClaimTemplate.spec.storageClassName`` 
           - 与生成的 Persistent Volume Claim 关联的 Kubernetes 存储类。

             如果不存在与指定值匹配的存储类，*或者* 指定的存储类无法满足所请求的 PVC 数量或存储容量，Tenant 可能无法启动。

         * - ``volumeClaimTemplate.spec.resources.requests.storage``
           - 为每个生成的 PVC 请求的存储容量。

   #. 配置 Tenant Affinity 或 Anti-Affinity

      MinIO Operator 支持以下 Kubernetes Affinity 和 Anti-Affinity 配置：

      - Node Affinity (``spec.pools[n].nodeAffinity``)
      - Pod Affinity (``spec.pools[n].podAffinity``)
      - Pod Anti-Affinity (``spec.pools[n].podAntiAffinity``)

      MinIO 建议为 Tenant 配置 Pod Anti-Affinity，以确保 Kubernetes 调度器不会将多个 pod 调度到同一个 worker node 上。

      如果你希望将 Tenant 部署到特定 worker node 上，请将对应的 node label 或过滤条件传入 ``nodeAffinity`` 字段，以约束调度器仅在这些节点上放置 pod。

   #. 配置网络加密

      MinIO Tenant CRD 提供了以下字段，你可以通过它们配置 Tenant 的 TLS 网络加密：

      .. list-table::
         :header-rows: 1
         :widths: 30 70

         * - 字段
           - 描述

         * - ``tenant.certificate.requestAutoCert``
           - 启用或禁用 MinIO :ref:`自动 TLS 证书生成 <minio-tls>`

             若省略该字段，默认值为 ``true``，即启用。

         * - ``tenant.certificate.certConfig``
           - 在启用的情况下，自定义 :ref:`自动 TLS <minio-tls>` 的行为。

         * - ``tenant.certificate.externalCertSecret``
           - 通过 Server Name Indication (SNI) 为多个主机名启用 TLS
         
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

   #. 检查命名空间

      YAML 对象 ``kind: Namespace`` 将 Tenant 的默认命名空间设置为 ``minio-tenant``。

      你可以修改该值，为 Tenant 创建不同的命名空间。
      你必须同时修改 YAML 文件中 **所有** ``metadata.namespace`` 的值，使其与该命名空间保持一致。

   #. 部署 Tenant

      使用 ``kubectl apply -f`` 命令部署 Tenant。

      .. code-block:: shell
         :class: copyable

         kubectl apply -f tenant-base.yaml

      该命令会在配置好的命名空间中创建 YAML 对象里定义的每一项资源。

      你可以使用以下命令监控进度：

      .. code-block:: shell
         :class: copyable

         watch kubectl get all -n minio-tenant

   #. 暴露 Tenant 的 MinIO S3 API 端口

      若要在本地机器上测试 MinIO Client :mc:`mc`，请转发 MinIO 端口并创建别名。

      * 转发 Tenant 的 MinIO 端口：

      .. code-block:: shell
         :class: copyable

         kubectl port-forward svc/MINIO_TENANT_NAME-hl 9000 -n MINIO_TENANT_NAMESPACE

      * 为 Tenant 服务创建别名：

      .. code-block:: shell
         :class: copyable

         mc alias set myminio https://localhost:9000 minio minio123 --insecure

      你可以使用 :mc:`mc mb` 在 Tenant 上创建存储桶：
      
      .. code-block:: shell
         :class: copyable

         mc mb myminio/mybucket --insecure

      如果你为 MinIO Tenant 部署的是由受信任 Certificate Authority (CA) 签发的 TLS 证书，则可以省略 ``--insecure`` 参数。
      
      具体说明请参阅 :ref:`create-tenant-connect-tenant`。

.. _create-tenant-connect-tenant:

连接到 Tenant
-------------

MinIO Operator 会为 MinIO Tenant 创建服务。


使用 ``kubectl get svc -n NAMESPACE`` 命令查看已部署的服务。
如果你的 Kubernetes 环境使用自定义的 ``kubectl`` 替代程序，也可以替换为对应程序名。

.. code-block:: shell
   :class: copyable

   kubectl get svc -n minio-tenant-1

.. code-block:: shell

   NAME                               TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
   minio                              LoadBalancer   10.97.114.60     <pending>     443:30979/TCP    2d3h
   TENANT-NAMESPACE-console           LoadBalancer   10.106.103.247   <pending>     9443:32095/TCP   2d3h
   TENANT-NAMESPACE-hl                ClusterIP      None             <none>        9000/TCP         2d3h

- ``minio`` 服务对应 MinIO Tenant 服务。
  应用程序应通过该服务对 MinIO Tenant 执行操作。
 
- ``*-console`` 服务对应 :minio-git:`MinIO Console <console>`。
  管理员应通过该服务访问 MinIO Console，并对 MinIO Tenant 执行管理操作。

其余服务用于支撑 Tenant 内部操作，并不面向用户或管理员直接使用。
 
默认情况下，每个服务仅在 Kubernetes 集群内部可见。
部署在集群内部的应用可以通过 ``CLUSTER-IP`` 访问这些服务。

位于 Kubernetes 集群外部的应用可以通过 ``EXTERNAL-IP`` 访问这些服务。
该值只有在 Kubernetes 集群配置了 Ingress 或类似网络访问服务时才会被填充。
Kubernetes 提供了多种对 service 开放外部访问的方式。

有关如何配置 service 的外部访问，请参阅 Kubernetes 文档中的 :kube-docs:`Publishing Services (ServiceTypes) <concepts/services-networking/service/#publishing-services-service-types>` 和 :kube-docs:`Ingress <concepts/services-networking/ingress/>`。

对于 OpenShift、Rancher 等特定 Kubernetes 发行版，请以其服务文档中关于对内或对外暴露 Service 的首选或可用方式为准。
