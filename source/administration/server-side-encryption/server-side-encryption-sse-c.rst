.. _minio-encryption-sse-c:

=======================================================
使用客户端管理密钥的服务端加密（SSE-C）
=======================================================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. |EK|  replace:: :abbr:`EK (外部密钥)`
.. |DEK| replace:: :abbr:`DEK (数据加密密钥)`
.. |KEK| replace:: :abbr:`KEK (密钥加密密钥)`
.. |OEK| replace:: :abbr:`OEK (对象加密密钥)`
.. |SSE| replace:: :abbr:`SSE (服务端加密)`
.. |KMS| replace:: :abbr:`KMS (密钥管理服务)`
.. |KES| replace:: :abbr:`KES (密钥加密服务)`

MinIO Server-Side Encryption (SSE) 在写入操作过程中保护对象，
使客户端能够利用服务端处理能力在存储层实现对象保护
（静态加密，encryption-at-rest）。SSE 还提供满足安全锁定与擦除相关
监管和合规要求所需的关键能力。

本页中的步骤用于配置并启用使用客户端管理密钥的服务端加密
（SSE-C）。MinIO SSE-C 支持由客户端在对象写入磁盘*之前*
驱动对象加密。客户端在执行读取操作时必须提供正确的密钥
才能解密对象。

MinIO SSE-C 在功能上兼容 Amazon
:s3-docs:`Server-Side Encryption with Customer-Provided Keys
<ServerSideEncryptionCustomerKeys.html>`. 

.. _minio-encryption-sse-c-erasure-locking:

安全擦除与锁定
----------------

SSE-C 在写入操作期间使用客户端指定的 |EK| 来保护对象。
前提是客户端侧的密钥管理支持禁用或删除这些密钥：

- 禁用 |EK| 会通过使使用该 |EK| 加密的对象变得不可读，
   从而暂时锁定这些对象。之后您可以重新启用该 |EK|，
   以恢复对这些对象的正常读取操作。

- 删除 |EK| 会使所有使用该 |EK| 加密的对象
   *永久* 不可读。如果客户端侧 KMS 不支持
   对 |EK| 进行备份，则该过程 *不可逆*。

单个 |EK| 的影响范围取决于在请求 SSE-C 加密时
有多少次写入操作指定了该 |EK|。 

注意事项
--------

复制场景中的 SSE-C
~~~~~~~~~~~~~~~~~~

.. versionchanged:: Server RELEASE.2024-03-30T09-41-56Z

   使用 SSE-C 加密的对象现在可以通过站点复制或存储桶复制进行复制。
   早期版本的 MinIO Object Store 不会复制经过 SSE-C 加密的对象。

经过压缩的 SSE-C 加密对象与 MinIO :ref:`bucket replication <minio-bucket-replication>` 或 :ref:`site replication <minio-site-replication-overview>` 不兼容。 
请使用 :ref:`SSE-KMS <minio-encryption-sse-kms>` 或 :ref:`SSE-S3 <minio-encryption-sse-s3>`，以确保加密对象与复制兼容。

SSE-C 会覆盖 SSE-S3 和 SSE-KMS
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 SSE-C 加密对象后，MinIO 将不会再对该对象应用 
:ref:`SSE-KMS <minio-encryption-sse-kms>` 或
:ref:`SSE-S3 <minio-encryption-sse-s3>` 加密。

快速开始
--------

MinIO SSE-C 要求客户端执行所有密钥创建和存储操作。

本流程使用 :mc:`mc` 对源 MinIO 部署执行操作。 
请在可访问该源部署网络的机器上安装 :mc:`mc`。 
有关下载和安装 ``mc`` 的说明，请参见 ``mc`` :ref:`Installation Quickstart <mc-install>`。

SSE-C 密钥 *必须* 是一个 256 位原始编码字符串或十六进制编码字符串。 
客户端应用负责生成并存储该加密密钥。
MinIO *不会* 存储 SSE-C 加密密钥，并且在没有客户端管理密钥的情况下无法解密 SSE-C 加密对象。

.. note::

   MinIO Client 从 ``RELEASE.2024-06-20T14-50-54Z`` 开始支持十六进制编码密钥。

1) 生成加密密钥
~~~~~~~~~~~~~~~

生成一个 256 位 base64 原始编码字符串或十六进制编码字符串作为加密密钥。

以下示例生成一个满足加密密钥要求的字符串。 
生成的字符串适用于非生产环境：

.. code-block:: shell
   :class: copyable

   cat /dev/urandom | head -c 32 | base64 -

请遵循您所在组织关于生成加密安全密钥的要求。

复制该加密密钥，以便在下一步中使用。

2) 使用 SSE-C 加密对象
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

MinIO 支持使用以下 AWS S3 请求头指定 SSE-C 加密：

- ``X-Amz-Server-Side-Encryption-Customer-Algorithm`` 设置为 ``AES256``。

- ``X-Amz-Server-Side-Encryption-Customer-Key`` 设置为加密密钥值。

- ``X-Amz-Server-Side-Encryption-Customer-Key-MD5`` 设置为加密密钥的 128 位 MD5 摘要。

MinIO :mc:`mc` 命令行工具及兼容 S3 的 SDK 提供了设置这些请求头的特定语法。
某些 :mc:`mc` 命令（例如 :mc:`mc cp`）包含用于启用 SSE-S3 加密的
专用参数：

.. code-block:: shell
   :class: copyable

   mc cp ~/data/mydata.json ALIAS/BUCKET/mydata.json \
      --encrypt-key "ALIAS/BUCKET/=c2VjcmV0ZW5jcnlwdGlvbmtleWNoYW5nZW1lMTIzNAo="

- 将 :mc-cmd:`ALIAS <mc encrypt set ALIAS>` 替换为您要写入
  SSE-C 加密对象的 MinIO 部署的 :mc:`alias <mc alias>`。

- 将 :mc-cmd:`BUCKET <mc encrypt set ALIAS>` 替换为您要写入
  SSE-C 加密对象的存储桶或存储桶前缀的完整路径。

3) 复制 SSE-C 加密对象
~~~~~~~~~~~~~~~~~~~~~~

MinIO 支持使用以下 AWS S3 请求头，将 SSE-C 加密对象复制到另一个兼容 S3 的服务：

- ``X-Amz-Copy-Source-Server-Side-Encryption-Algorithm`` 设置为 ``AES256``

- ``X-Amz-Copy-Source-Server-Side-Encryption-Key`` 设置为加密密钥值。
  如果指定的密钥与用于对该对象执行 SSE-C 加密的密钥不匹配，
  复制操作将失败。

- ``X-Amz-Copy-Source-Server-Side-Encryption-Key-MD5`` 设置为加密密钥的 128 位 MD5
  摘要。

MinIO :mc:`mc` 命令行工具及兼容 S3 的 SDK 提供了设置这些请求头的特定语法。
某些 :mc:`mc` 命令（例如 :mc:`mc cp`）包含用于启用 SSE-S3 加密的
专用参数：

.. code-block:: shell
   :class: copyable

   mc cp SOURCE/BUCKET/mydata.json TARGET/BUCKET/mydata.json  \
   --encrypt-key "SOURCE/BUCKET/=c2VjcmV0ZW5jcnlwdGlvbmtleWNoYW5nZW1lMTIzNAo=,TARGET/BUCKET/=c2VjcmV0ZW5jcnlwdGlvbmtleWNoYW5nZW1lMTIzNAo="

- 将 :mc-cmd:`SOURCE/BUCKET <mc encrypt set ALIAS>` 替换为您要读取
  加密对象所在的 MinIO 部署的 :mc:`alias <mc alias>`，
  以及您要读取 SSE-C 加密对象的存储桶或存储桶前缀的完整路径。

- 将 :mc-cmd:`TARGET/BUCKET <mc encrypt set ALIAS>` 替换为您要写入
  加密对象的 MinIO 部署的 :mc:`alias <mc alias>`，
  以及您要写入 SSE-C 加密对象的存储桶或存储桶前缀的完整路径。
