.. _minio-sse-data-encryption:

================
数据加密（SSE）
================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. |EK| replace:: :abbr:`EK (External Key)`
.. |SSE| replace:: :abbr:`SSE (Server-Side Encryption)`
.. |KMS| replace:: :abbr:`KMS (Key Management System)`

MinIO Server-Side Encryption (SSE) 在对象写入过程中为其提供保护，
使客户端能够利用服务端的处理能力，
在存储层实现对象静态加密（encryption-at-rest）。
SSE 还为围绕安全锁定和擦除的监管与合规要求提供关键能力。

MinIO SSE 使用 :minio-git:`MinIO Key Encryption Service (KES) <kes>` 和外部 Key Management Service (KMS)，
以大规模执行安全的密码学操作。
MinIO 还支持由客户端管理密钥的模式，
在这种模式下，应用程序完全负责创建和管理供 MinIO SSE 使用的加密密钥。

MinIO 支持将以下 |KMS| 作为中心密钥存储：

- :ref:`HashiCorp KeyVault <minio-sse-vault>`
- :ref:`AWS SecretsManager <minio-sse-aws>`
- :ref:`Google Cloud SecretManager <minio-sse-gcp>`
- :ref:`Azure Key Vault <minio-sse-azure>`
- :minio-git:`Fortanix SDKMS <kes/wiki/Fortanix-SDKMS>`
- :minio-git:`Thales Digital Identity and Security (formerly Gemalto) <kes/wiki/Gemalto-KeySecure>`

MinIO SSE 需要启用 :ref:`minio-tls`。

支持的加密类型
--------------

MinIO SSE 在功能和 API 上兼容
:s3-docs:`AWS Server-Side Encryption <server-side-encryption.html>`，
并支持以下加密策略：

.. tab-set::

   .. tab-item:: SSE-KMS *Recommended*
      :sync: sse-kms

      MinIO 支持使用存储在外部 |KMS| 中的特定 External Key (EK)，
      对写入某个存储桶的所有对象自动启用 SSE-KMS 加密。
      客户端可以在写入操作中显式指定密钥，
      以覆盖存储桶默认使用的 |EK|。

      对于未启用自动 SSE-KMS 加密的存储桶，
      客户端也可以在写入操作中指定 |EK|。

      启用服务端加密时，MinIO 也会加密后端数据。
      SSE-KMS 一旦启用便无法禁用。

      相比 SSE-S3 和 SSE-C，
      SSE-KMS 提供更细粒度、可定制性更强的加密能力，
      因此推荐优先使用。

      关于如何在本地（非生产）MinIO 部署中启用 SSE-KMS，
      请参见 :ref:`minio-encryption-sse-kms-quickstart`。

   .. tab-item:: SSE-S3
      :sync: sse-s3

      MinIO 支持使用存储在外部 |KMS| 中的 |EK|，
      对写入某个存储桶的所有对象自动启用 SSE-S3 加密。
      MinIO SSE-S3 在整个部署范围内仅支持 *一个* |EK|。

      对于未启用自动 SSE-S3 加密的存储桶，
      客户端也可以在写入操作中请求使用 SSE 加密。

      启用服务端加密时，MinIO 也会加密后端数据。
      SSE-KMS 一旦启用便无法禁用。

      关于如何在本地（非生产）MinIO 部署中启用 SSE-S3，
      请参见 :ref:`minio-encryption-sse-s3-quickstart`。

   .. tab-item:: SSE-C
      :sync: sse-c

      客户端在对象写入操作中指定 |EK|。
      MinIO 使用指定的 |EK| 执行 SSE-S3。

      SSE-C 不支持存储桶默认加密设置，
      并要求客户端自行执行全部密钥管理操作。

.. toctree::
   :titlesonly:
   :hidden:

   /operations/server-side-encryption/configure-minio-kes
