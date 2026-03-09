.. _minio-sse-vault:
.. _minio-sse-gcp:
.. _minio-sse-azure:
.. _minio-sse-aws:

=============================
使用 KES 进行服务端对象加密
=============================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 1

.. |EK|            replace:: :abbr:`EK (External Key)`
.. |SSE|           replace:: :abbr:`SSE (Server-Side Encryption)`
.. |KMS|           replace:: :abbr:`KMS (Key Management System)`
.. |KES-git|       replace:: :minio-git:`Key Encryption Service (KES) <kes>`
.. |KES|           replace:: :abbr:`KES (Key Encryption Service)`
.. |rootkms|       replace:: `HashiCorp Vault <https://vaultproject.io/>`__
.. |rootkms-short| replace:: Vault

.. meta::
   :description: Deploy MinIO with Server-Side Object Encryption
   :keywords: encryption, security, hashicorp, keyvault, azure

.. Conditionals to handle the slight divergences in procedures between platforms.

.. tab-set::
   :class: parent

   .. tab-item:: Kubernetes
      :sync: k8s

      本流程假定你可以访问一个已经安装并启用了 MinIO Operator 的 Kubernetes 集群。
      关于如何运行 KES，请参见 :kes-docs:`KES 文档 <tutorials/getting-started/>`。

      在本流程中，你将完成：

      #. 创建或修改一个通过 |KES| 支持 |SSE| 的 MinIO 部署。
         关于生产可用 MinIO 部署的指导，请参见 :ref:`部署分布式 MinIO <minio-mnmd>` 教程。

      #. 使用 MinIO Operator Console 创建或管理一个 MinIO 租户。
      #. 进入该租户的 :guilabel:`Encryption` 设置，并通过 :kes-docs:`受支持的 Key Management System <#supported-kms-targets>` 配置 |SSE|。
      #. 创建一个新的 |EK| 供 |SSE| 使用。
      #. 配置自动化的存储桶默认 :ref:`SSE-KMS <minio-encryption-sse-kms>`。

   .. tab-item:: Baremetal
      :sync: baremetal

      本流程说明如何部署已配置 KES 并启用 :ref:`服务端加密 <minio-sse-data-encryption>` 的 MinIO。
      关于如何运行 KES，请参见 :kes-docs:`KES 文档 <tutorials/getting-started/>`。

      在本流程中，你将完成：

      #. 创建一个新的 |EK| 供 |SSE| 使用。

      #. 创建或修改一个通过 |KES| 支持 |SSE| 的 MinIO 部署。
         关于生产可用 MinIO 部署的指导，请参见 :ref:`部署分布式 MinIO <minio-mnmd>` 教程。

      #. 配置自动化的存储桶默认 :ref:`SSE-KMS <minio-encryption-sse-kms>`

.. important::

   .. include:: /includes/common/common-minio-kes.rst
      :start-after: start-kes-encrypted-backend-desc
      :end-before: end-kes-encrypted-backend-desc

前提条件
--------

访问 MinIO 集群
~~~~~~~~~~~~~~~

.. tab-set::
   

   .. tab-item:: Kubernetes
      :sync: k8s

      你必须能够访问 Kubernetes 集群，并且 ``kubectl`` 上下文配置的权限至少具备管理员级别。

      本流程假定你的权限集足以支持在 Kubernetes 集群中部署或修改与 MinIO 相关的资源，包括但不限于 pods、statefulsets、replicasets、deployments 和 secrets。

   .. tab-item:: Baremetal
      :sync: baremetal

      本流程使用 :mc:`mc` 对 MinIO 集群执行操作。
      请在一台能够访问该集群网络的机器上安装 ``mc``。
      关于如何下载和安装 ``mc``，请参见 ``mc`` :ref:`安装快速开始 <mc-install>`。

      本流程假定已为 MinIO 集群配置 :mc:`alias <mc alias>`。

.. _minio-sse-vault-prereq-vault:

确保 KES 可访问受支持的 KMS 目标
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tab-set::
   

   .. tab-item:: Kubernetes
      :sync: k8s

      本流程假定已经存在一个可从 Kubernetes 集群访问的 :kes-docs:`受支持 KMS 安装 <#supported-kms-targets>`。

      - 对于与 MinIO 租户位于同一 Kubernetes 集群的部署，你可以使用 Kubernetes service 名称，让 MinIO 租户连接到目标 KMS 服务。

      - 对于位于 Kubernetes 集群外部的部署，你必须确保该集群支持 Kubernetes services、pods 与外部网络之间的通信路由。
        这可能需要配置或部署额外的 Kubernetes 网络组件，和/或启用访问公网的能力。

      关于部署和配置的指导，请以你所选 KMS 方案的文档为准。

   .. tab-item:: Baremetal
      :sync: baremetal

      本流程假定已经存在一个 KES 安装，并已连接到受支持的 |KMS| 安装，且二者都可从本地主机访问。
      请参照你所选 :kes-docs:`受支持 KMS 目标 <#supported-kms-targets>` 的安装说明，部署 KES 并将其连接到对应 KMS 方案。
   
.. admonition:: KES 操作要求目标处于 Unsealed 状态
   :class: important

   某些受支持的 |KMS| 目标允许你对 Vault 实例执行 seal 或 unseal。
   如果已配置的 |KMS| 服务处于 sealed 状态，KES 会返回错误。

   如果你重启或以其他方式 seal 了 Vault 实例，KES 将无法针对该 Vault 执行任何密码学操作。
   你必须对 Vault 执行 unseal，才能确保其正常运行。

   关于是否需要执行 unseal 的更多信息，请参见你所选 |KMS| 方案的文档。

对于你所选的受支持 |KMS|，请参照 :kes-docs:`KES 文档 <>` 中的配置说明：

- :kes-docs:`AWS Secrets Manager <integrations/aws-secrets-manager/>`
- :kes-docs:`Azure KeyVault <integrations/azure-keyvault/>`
- :kes-docs:`Entrust KeyControl <integrations/entrust-keycontrol/>`
- :kes-docs:`Fortanix SDKMS <integrations/fortanix-sdkms/>`
- :kes-docs:`Google Cloud Secret Manager <integrations/google-cloud-secret-manager/>`
- :kes-docs:`HashiCorp Vault <integrations/hashicorp-vault-keystore/>`
- :kes-docs:`Thales CipherTrust Manager (formerly Gemalto KeySecure) <integrations/thales-ciphertrust/>`

流程
----

本流程说明如何在生产环境中使用你所选的 `受支持 KMS 方案 <https://docs.min.io/community/minio-kes/#supported-kms-targets>`__ 配置并启用服务端加密。
具体来说，本流程假定满足以下条件：

- 已有一个生产级 KMS 目标
- 一个或多个已连接到该 KMS 目标的 KES 服务器
- 一个或多个用于新建或现有 MinIO 部署的主机

.. tab-set::
   

   .. tab-item:: Kubernetes
      :sync: k8s

      .. include:: /includes/k8s/steps-configure-minio-kes-hashicorp.rst

   .. tab-item:: Baremetal
      :sync: baremetal

      .. include:: /includes/linux/steps-configure-minio-kes-hashicorp.rst
