.. _minio-sse:
.. _minio-encryption-overview:

=================================
对象的服务器端加密
=================================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 1

.. |EK| replace:: :abbr:`EK (外部密钥)`
.. |SSE| replace:: :abbr:`SSE (服务器端加密)`
.. |KMS| replace:: :abbr:`KMS (密钥管理系统)`

MinIO 服务器端加密（SSE）在写入操作期间保护对象，使客户端能够利用服务器的处理能力在存储层保障对象安全（静态加密）。
SSE 还为与安全锁定和擦除相关的监管与合规要求提供关键能力。

MinIO SSE 使用 :kes-docs:`MinIO Key Encryption Service (KES) <>` 和外部密钥管理服务（KMS）来大规模执行安全的加密操作。
MinIO 也支持客户端管理的密钥管理模式，由应用程序全权负责创建和管理供 MinIO SSE 使用的加密密钥。

MinIO SSE 在功能和 API 上与 :s3-docs:`AWS Server-Side Encryption <server-side-encryption.html>` 兼容，并支持以下加密策略：

.. tab-set::

   .. tab-item:: SSE-KMS *推荐*
      :sync: sse-kms

      MinIO 支持使用存储在外部 |KMS| 上的特定外部密钥（EK），为写入某个存储桶的所有对象启用自动 SSE-KMS 加密。
      客户端可以在写入操作中指定显式密钥，以覆盖存储桶默认的 |EK|。

      对于未启用自动 SSE-KMS 加密的存储桶，客户端也可以在写入操作时指定一个 |EK|。

      MinIO 会在启用服务器端加密时对后端数据进行加密。
      SSE-KMS 加密一旦启用便无法禁用。

      与 SSE-S3 和 SSE-C 相比，SSE-KMS 提供更细粒度且可定制的加密能力，因此更推荐使用这种方式，而不是其他受支持的加密方法。

      如需在本地（非生产）MinIO 部署中启用 SSE-KMS 的教程，请参阅 :ref:`minio-encryption-sse-kms-quickstart`。
      对于生产环境的 MinIO 部署，请使用以下指南之一：

      - :kes-docs:`AWS Secrets Manager <integrations/aws-secrets-manager/>`
      - :kes-docs:`Azure Key Vault <integrations/azure-keyvault/>`
      - :kes-docs:`Entrust KeyControl <integrations/entrust-keycontrol/>`
      - :kes-docs:`Fortanix SDKMS <integrations/fortanix-sdkms/>`
      - :kes-docs:`Google Cloud Secret Manager <integrations/google-cloud-secret-manager/>`
      - :kes-docs:`HashiCorp Vault Keystore <integrations/hashicorp-vault-keystore/>`
      - :kes-docs:`Thales CipherTrust Manager (formerly Gemalto KeySecure) <integrations/thales-ciphertrust/>`

   .. tab-item:: SSE-S3
      :sync: sse-s3

      MinIO 支持使用存储在外部 |KMS| 上的一个 |EK|，为写入某个存储桶的所有对象
      启用自动 SSE-S3 加密。MinIO SSE-S3 在整个部署范围内仅支持 *一个* |EK|。

      对于未启用自动 SSE-S3 加密的存储桶，客户端也可以在写入操作中请求
      SSE 加密。

      MinIO 会在启用服务器端加密时对后端数据进行加密。
      SSE-KMS 加密一旦启用便无法禁用。

      如需在本地（非生产）MinIO 部署中启用 SSE-s3 的教程，请参阅
      :ref:`minio-encryption-sse-s3-quickstart`。对于生产环境的 MinIO 部署，请使用以下指南之一：

      - :kes-docs:`AWS Secrets Manager <integrations/aws-secrets-manager/>`
      - :kes-docs:`Azure Key Vault <integrations/azure-keyvault/>`
      - :kes-docs:`Entrust KeyControl <integrations/entrust-keycontrol/>`
      - :kes-docs:`Fortanix SDKMS <integrations/fortanix-sdkms/>`
      - :kes-docs:`Google Cloud Secret Manager <integrations/google-cloud-secret-manager/>`
      - :kes-docs:`HashiCorp Vault Keystore <integrations/hashicorp-vault-keystore/>`
      - :kes-docs:`Thales CipherTrust Manager (formerly Gemalto KeySecure) <integrations/thales-ciphertrust/>`

   .. tab-item:: SSE-C
      :sync: sse-c

      客户端在对象写入操作中指定一个 |EK|。
      MinIO 使用指定的 |EK| 执行 SSE-S3。

      SSE-C 不支持存储桶默认加密设置，并要求客户端执行所有密钥管理操作。

MinIO SSE 需要启用 :ref:`minio-tls`。

.. _minio-encryption-sse-secure-erasure-locking:

安全擦除与锁定
--------------------------

MinIO 需要访问用于 SSE 操作的加密密钥（EK）*以及* 外部密钥管理系统
（KMS）才能解密对象。你可以利用这一依赖关系，通过禁用对用于加密的
EK 或 KMS 的访问，来安全地擦除对象并锁定对其的访问。

常见策略包括但不限于：

- Seal |KMS| 使 MinIO Server 无法再访问它。这样会锁定所有由存储在
  KMS 上的任意 |EK| 保护的 SSE-KMS 或 SSE-S3 加密对象。只要 KMS
  保持 sealed，这些加密对象就始终不可读。

- Seal/Unmount 一个 |EK|。这样会锁定所有由该 EK 保护的 SSE-KMS 或
  SSE-S3 加密对象。只要 CMK(s) 处于 sealed 状态，这些加密对象就始终不可读。

- 删除一个 |EK|。这样会使所有由该 EK 保护的 SSE-KMS 或 SSE-S3 加密对象
  永久不可读。删除 EK 并同时删除数据的组合方式，可能满足围绕数据安全删除的
  监管要求。

  删除一个 |EK| 通常是不可逆的。在有意删除主密钥之前务必极其谨慎。

如需了解更多信息，请参阅：

- :ref:`SSE-KMS 安全擦除与锁定
  <minio-encryption-sse-kms-erasure-locking>`

- :ref:`SSE-S3 安全擦除与锁定
  <minio-encryption-sse-s3-erasure-locking>`

- :ref:`SSE-C 安全擦除与锁定
  <minio-encryption-sse-c-erasure-locking>`

.. toctree::
   :titlesonly:
   :hidden:

   /administration/server-side-encryption/server-side-encryption-sse-kms
   /administration/server-side-encryption/server-side-encryption-sse-s3
   /administration/server-side-encryption/server-side-encryption-sse-c
