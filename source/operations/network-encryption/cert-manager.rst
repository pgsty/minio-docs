.. _minio-certmanager:

================
cert-manager
================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 1

使用 cert-manager 管理 TLS 证书
-------------------------------

本指南介绍如何安装 cert-manager 以管理 TLS 证书。
本文假设你使用的是全新或刚初始化的 MinIO Operator 安装环境。

.. note::

   本指南使用自签名 ``ClusterIssuer``。
   你也可以使用 `cert-manager 支持的其他 Issuer <https://cert-manager.io/docs/configuration/issuers/>`__。

   主要区别在于，你必须向 MinIO 提供该 ``Issuer`` 的 CA 证书，而不是本文中提到的这些 CA。

如需更高级的配置，请参考 `cert-manager 文档 <https://cert-manager.io>`__ 以及你所在组织的证书规范。

cert-manager 负责管理 Kubernetes 集群中的证书。
MinIO Operator 支持使用 cert-manager 来管理和签发证书，作为 Operator 自行管理自身及其租户证书的替代方案。

cert-manager 从 ``Issuer`` 或 ``ClusterIssuer`` 获取有效证书，并能在证书过期前自动续期。

``ClusterIssuer`` 可为多个命名空间签发证书。
``Issuer`` 只能为其所在命名空间签发证书。

下图展示了 cert-manager 如何在 Kubernetes 集群的不同命名空间中提供证书。

- ``ClusterIssuer`` 位于 Kubernetes 集群的根级别，通常在 ``default`` 命名空间中，用于向其他所有命名空间提供证书。
- ``minio-operator`` 命名空间拥有其本地 ``Issuer``。
- 每个租户命名空间也拥有其本地 ``Issuer``。
- 各租户命名空间签发的证书必须被 MinIO Operator 感知并信任。

.. image:: /images/k8s/cert-manager-graph.png
   :width: 600px
   :alt: Kubernetes 集群命名空间关系图，展示根级 ClusterIssuer 与三个拥有各自 Issuer 的命名空间之间的关系。
   :align: center


前提条件
--------

- 使用受支持版本的 `Kubernetes <https://kubernetes.io/releases/>`__
- 已安装 `kustomize <https://kustomize.io/>`__
- 可通过 ``kubectl`` 访问你的 ``k8s`` 集群

.. _minio-setup-certmanager:

配置 cert-manager
-----------------

安装 cert-manager
~~~~~~~~~~~~~~~~~

以下命令使用 ``kubectl`` 安装 1.12.13 版本。

.. code-block:: shell
   :class: copyable
   
   kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.12.13/cert-manager.yaml

推荐使用 `Release 1.12.X LTS <https://cert-manager.io/docs/releases/release-notes/release-notes-1.12/>`__，但你也可以安装最新版本。
有关安装 cert-manager 的更多细节，请参见其 `installation instructions <https://cert-manager.io/docs/installation/>`__。

.. _minio-cert-manager-create-cluster-issuer:

为集群创建自签名 ClusterIssuer
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

``ClusterIssuer`` 是集群中的顶层 Issuer，其他所有证书都从它派生。

1. 创建一个 ``ClusterIssuer`` 资源，请求 cert-manager 生成该对象。

   创建一个名为 ``selfsigned-root-clusterissuer.yaml`` 的文件，内容如下：

   .. code-block:: yaml
      :class: copyable
   
      # selfsigned-root-clusterissuer.yaml
      apiVersion: cert-manager.io/v1
      kind: ClusterIssuer
      metadata:
        name: selfsigned-root
      spec:
        selfSigned: {}

2. 将该资源应用到集群：

   .. code-block:: shell
      :class: copyable

      kubectl apply -f selfsigned-root-clusterissuer.yaml

后续步骤
--------

继续配置 :ref:`用于 MinIO Operator 的 cert-manager <minio-certmanager-operator>`。

.. toctree::
   :titlesonly:
   :hidden:

   /operations/cert-manager/cert-manager-operator
   /operations/cert-manager/cert-manager-tenants
