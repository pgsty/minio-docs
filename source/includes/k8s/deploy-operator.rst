.. _minio-operator-installation:
.. _minio-operator-installation-kustomize:
.. _deploy-operator-kubernetes:
.. _deploy-operator-kubernetes-kustomize:

=========================
部署 MinIO Operator
=========================

.. default-domain:: minio

.. contents:: Table of Contents
   :local:
   :depth: 1

概述
----

MinIO 是面向 Kubernetes 的高性能对象存储，兼容 S3 API。
MinIO Kubernetes Operator 支持将 MinIO Tenant 部署到私有云和公有云基础设施中，也就是“混合”云环境。

以下流程用于在 Kubernetes 基础设施上安装最新稳定版（|operator-version-stable|）的 MinIO Operator。

MinIO Operator 会安装
:kube-docs:`Custom Resource Definition (CRD) <concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions>`，
以便将 MinIO 租户描述为 Kubernetes :kube-docs:`object <concepts/overview/working-with-objects/kubernetes-objects/>`。
关于 MinIO CRD 的完整文档，请参阅 MinIO Operator
:minio-git:`CRD Reference <operator/blob/master/docs/tenant_crd.adoc>`。

本文档假定你已经熟悉其中引用的 Kubernetes 概念、工具和流程。
虽然本文档会尽力为 Kubernetes 相关资源的配置和部署提供指导，
但它不能替代官方 :kube-docs:`Kubernetes Documentation <>`。

.. cond:: openshift

   .. important::

      通过 RedHat Marketplace 或 OperatorHub 部署 MinIO Operator 的支持已于 2024 年移除。
      MinIO AIStor 仍完整支持通过 Marketplace 和 OperatorHub
      安装到 OpenShift Container Platform (OCP) 等企业级 RedHat Kubernetes 发行版上。
      |subnet| 客户可以提交 issue，
      以获取进一步说明以及迁移到 `AIStor <https://min.io/product/aistor-overview?jmp=docs>`__ 的指导。

      本文档提供的是在 Kubernetes 基础设施上安装 operator 的通用方法。

MinIO Operator 组件
-------------------

MinIO Operator 运行在其自身命名空间中，并在其中创建 Kubernetes 资源。
这些资源包括 pod、service、replicaset 和 deployment。

默认情况下，Operator pod 会在所有命名空间中监控使用 MinIO CRD 的对象，
并自动管理这些资源。

当你使用 Operator 创建租户时，该租户*必须*拥有自己的命名空间。
在该命名空间中，Operator 会根据租户配置生成所需的 pod。

每个 Tenant pod 会运行三个容器：

- MinIO Container，负责运行标准 MinIO 功能，
  相当于裸机环境中的基础 MinIO 安装。
  该容器会在提供的挂载点（持久卷）上存储和读取对象。

- InitContainer，仅在 pod 启动期间存在，
  用于在启动时管理配置 secret。
  启动完成后，该容器会终止。

- Sidecar container，用于初始化 MinIO 租户。
  Sidecar 会为每个租户拉取并校验配置，
  并在 pod 中创建所需的本地资源。

  .. versionchanged:: Operator 6.0.0
     
     Sidecar 拥有独立于 MinIO Operator 其余部分的镜像和发布周期。
     MinIO Operator 会将租户的环境变量存储在 sidecar 中，
     因而可以在不触发滚动重启的情况下更新这些变量。

租户通过 Persistent Volume Claim 与用于存储对象的 Persistent Volume 交互。

.. Image references Console pods, need to fix this up

.. .. image:: /images/k8s/OperatorsComponent-Diagram.png
..    :width: 600px
..    :alt: A diagram of the namespaces and pods used by or maintained by the MinIO Operator.
..    :align: center

.. _minio-operator-prerequisites:

前提条件
--------

Kubernetes 版本 |k8s-floor|
~~~~~~~~~~~~~~~~~~~~~~~~~~~

MinIO 会在 Kubernetes API 最低版本为 |k8s-floor| 的环境上测试 |operator-version-stable|。
MinIO **强烈建议** 使用
`仍在积极维护中的 Kubernetes API 版本 <https://kubernetes.io/releases/>`__
来维护 Kubernetes 基础设施。

MinIO **强烈建议** 升级仍在使用
`已结束生命周期 API 版本 <https://kubernetes.io/releases/patch-releases/#non-active-branch-history>`__
的 Kubernetes 集群。

Kustomize 与 ``kubectl``
~~~~~~~~~~~~~~~~~~~~~~~~

`Kustomize <https://kubernetes.io/docs/tasks/manage-kubernetes-objects/kustomization>`__
是基于 YAML 的模板化工具，可让你以声明式、可重复的方式定义 Kubernetes 资源。
Kustomize 已包含在 :kube-docs:`kubectl <reference/kubectl>` 命令行工具中。

本流程假定你的本地主机既安装了与 Kubernetes 集群匹配版本的 ``kubectl``，
也具备在该集群中创建新资源所需的访问权限。

`默认的 MinIO Operator Kustomize 模板 <https://github.com/minio/operator/blob/master/kustomization.yaml>`__
可作为为本地环境定制配置的起点。
你可以修改默认 Kustomization 文件，
也可以应用自己的 `patch <https://datatracker.ietf.org/doc/html/rfc6902>`__
来定制 Kubernetes 集群中的 Operator 部署。

.. _minio-k8s-deploy-operator-tls:

Kubernetes TLS Certificate API
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. versionchanged:: Operator v.5.0.0

   MinIO Operator 使用 Kubernetes ``certificates.k8s.io``
   :kube-docs:`TLS certificate management API <tasks/tls/managing-tls-in-a-cluster/>`
   来管理 TLS Certificate Signing Request (CSR)，
   并在以下情况下创建已签名的 TLS 证书：
   
   - 启用 ``autoCert`` 时。
   - 当环境变量 :envvar:`MINIO_CONSOLE_TLS_ENABLE` 设置为 ``on`` 时，用于 MinIO Tenant Console。
   - 当环境变量 :envvar:`OPERATOR_STS_ENABLED` 设置为 ``on`` 时，用于 :ref:`STS 服务 <minio-security-token-service>`。
   - 用于获取集群健康状态。
   
   从 Operator 6.0.0 开始，MinIO Operator 会读取 ``operator-ca-tls`` secret 中的证书，
   以信任整个 Kubernetes 集群内的私有 CA，例如使用 cert-manager 时。
   早期版本的 Operator 会将 ``operator-ca-tls`` 证书同步到每个租户。

在上述任一场景中，MinIO Operator *都要求* Kubernetes ``kube-controller-manager``
配置中包含以下 :kube-docs:`配置项 <reference/command-line-tools-reference/kube-controller-manager/#options>`：

- ``--cluster-signing-key-file`` - 指定用于签发集群级证书的 PEM 编码 RSA 或 ECDSA 私钥。

- ``--cluster-signing-cert-file`` - 指定用于签发集群级证书的 PEM 编码 x.509 Certificate Authority 证书。

Kubernetes TLS API 会使用 CA 的签名算法来生成新的 TLS 证书。
相比 RSA，ECDSA（例如 `NIST P-256 curve <https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf>`__）
或 EdDSA（例如 :rfc:`Curve25519 <7748>`）TLS 私钥/证书的计算开销更低，
因此 MinIO 推荐使用这两类方案。
受支持 TLS Cipher Suite 的完整列表请参阅 :ref:`minio-TLS-supported-cipher-suites`。

如果 Kubernetes 集群未配置为响应生成的 :abbr:`CSR (Certificate Signing Request)`，
Operator 将无法完成初始化。
某些 Kubernetes 提供方默认不会设置这些配置值。

要检查 ``kube-controller-manager`` 是否指定了集群签名密钥和证书文件，
请使用以下命令：

.. code-block:: shell
   :class: copyable

   kubectl get pod kube-controller-manager-$CLUSTERNAME-control-plane \ 
     -n kube-system -o yaml

- 将 ``$CLUSTERNAME`` 替换为 Kubernetes 集群名称。

确认输出中包含高亮显示的行。
上面示例命令的输出可能与你终端中的实际输出不同：

.. code-block:: shell
   :emphasize-lines: 12,13

    spec:
    containers:
    - command:
        - kube-controller-manager
        - --allocate-node-cidrs=true
        - --authentication-kubeconfig=/etc/kubernetes/controller-manager.conf
        - --authorization-kubeconfig=/etc/kubernetes/controller-manager.conf
        - --bind-address=127.0.0.1
        - --client-ca-file=/etc/kubernetes/pki/ca.crt
        - --cluster-cidr=10.244.0.0/16
        - --cluster-name=my-cluster-name
        - --cluster-signing-cert-file=/etc/kubernetes/pki/ca.crt
        - --cluster-signing-key-file=/etc/kubernetes/pki/ca.key
    ...

.. important::

   MinIO Operator 会使用指定的 Certificate Authority (CA)
   自动为所有 MinIO Tenant pod 生成 TLS 证书。
   Kubernetes 集群外部的客户端若要连接 MinIO Operator 或 MinIO Tenant，
   必须信任该 Kubernetes 集群 CA。

   无法信任 Kubernetes 集群 CA 的客户端，
   可以禁用到 MinIO Operator 或 MinIO Tenant 连接的 TLS 校验。

   另一种做法是生成由已知可信 CA 签名的 x.509 TLS 证书，
   并将这些证书传递给 MinIO Tenant。
   更完整的文档请参阅 :ref:`minio-tls`。

使用 cert-manager 管理证书
~~~~~~~~~~~~~~~~~~~~~~~~~~

你也可以将部署配置为使用 `cert-manager <https://cert-manager.io/>`__，
而不是由 MinIO Operator 自行管理证书。
关于如何使用 cert-manager 部署 MinIO Operator 和租户，
请参阅 :ref:`cert-manager 页面 <minio-certmanager>`。

流程
----

以下步骤使用 Kustomize 以及 MinIO Operator GitHub 仓库中的 ``kustomization.yaml``
文件来部署 Operator。
如果要使用 Helm chart 安装 Operator，请参阅
:ref:`使用 Helm 部署 Operator <minio-k8s-deploy-operator-helm>`。

.. include:: /includes/common/common-install-operator-kustomize.rst

.. toctree::
   :titlesonly:
   :hidden:

   /operations/install-deploy-manage/deploy-operator-helm
   
