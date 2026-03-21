.. _minio-encryption-sse-s3:

==================================================
部署级服务端加密密钥（SSE-S3）
==================================================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 1

.. |EK|  replace:: :abbr:`EK (External Key)`
.. |DEK| replace:: :abbr:`DEK (Data Encryption Key)`
.. |KEK| replace:: :abbr:`KEK (Key Encryption Key)`
.. |OEK| replace:: :abbr:`OEK (Object Encryption Key)`
.. |SSE| replace:: :abbr:`SSE (Server-Side Encryption)`
.. |KMS| replace:: :abbr:`KMS (Key Management Service)`
.. |KES| replace:: :abbr:`KES (Key Encryption Service)`

MinIO 服务端加密（SSE）在写入操作期间保护对象，使客户端能够利用服务端的处理能力，
在存储层保护对象（静态加密）。SSE 还为围绕安全锁定和擦除的监管与合规要求提供关键能力。

MinIO SSE 使用 :kes-docs:`MinIO Key Encryption Service (KES) <>` 和外部
Key Management Service (KMS) 以安全方式大规模执行加密操作。MinIO 还支持
客户端自主管理密钥，即由应用全权负责创建和管理供 MinIO SSE 使用的加密密钥。

MinIO SSE-S3 使用由 Key Management System (KMS) 管理的 |EK| 对对象进行加/解密。
你必须在启动 MinIO 服务器时通过 :envvar:`MINIO_KMS_KES_KEY_NAME` 环境变量指定
该 |EK|。对于 *所有* SSE-S3 加密操作，MinIO 都使用同一个 EK。

你可以使用 :mc:`mc encrypt set` 命令启用存储桶默认 SSE-S3 加密：

.. code-block:: shell
   :class: copyable

   mc encrypt set sse-s3 play/mybucket

- 将 ``play/mybucket`` 替换为你要启用自动 SSE-KMS 加密的 :mc:`alias <mc alias>`
  和存储桶。

MinIO SSE-S3 在功能上兼容 AWS S3
:s3-docs:`Server-Side Encryption with Amazon S3-Managed Keys <UsingServerSideEncryption.html>`，
同时将支持扩展到以下 KMS 提供商：

- :kes-docs:`AWS Secrets Manager <integrations/aws-secrets-manager/>`
- :kes-docs:`Azure KeyVault <integrations/azure-keyvault/>`
- :kes-docs:`Entrust KeyControl <integrations/entrust-keycontrol/>`
- :kes-docs:`Fortanix SDKMS <integrations/fortanix-sdkms/>`
- :kes-docs:`Google Cloud Secret Manager <integrations/google-cloud-secret-manager/>`
- :kes-docs:`HashiCorp Vault <integrations/hashicorp-vault-keystore/>`
- :kes-docs:`Thales CipherTrust Manager (formerly Gemalto KeySecure) <integrations/thales-ciphertrust/>`

.. _minio-encryption-sse-s3-quickstart:

快速开始
----------

.. important::

   .. include:: /includes/common/common-minio-kes.rst
      :start-after: start-kes-encrypted-backend-desc
      :end-before: end-kes-encrypted-backend-desc

以下流程使用 ``play`` MinIO |KES| 沙箱，在评估和早期开发环境中为 SSE-S3 提供
|SSE| 支持。

对于较长期的开发环境或生产环境，请使用以下受支持的外部
Key Management Services (KMS) 之一：

- :kes-docs:`AWS Secrets Manager <integrations/aws-secrets-manager/>`
- :kes-docs:`Azure KeyVault <integrations/azure-keyvault/>`
- :kes-docs:`Entrust KeyControl <integrations/entrust-keycontrol/>`
- :kes-docs:`Fortanix SDKMS <integrations/fortanix-sdkms/>`
- :kes-docs:`Google Cloud Secret Manager <integrations/google-cloud-secret-manager/>`
- :kes-docs:`HashiCorp Vault <integrations/hashicorp-vault-keystore/>`
- :kes-docs:`Thales CipherTrust Manager (formerly Gemalto KeySecure) <integrations/thales-ciphertrust/>`

.. include:: /includes/common/common-minio-kes.rst
   :start-after: start-kes-play-sandbox-warning
   :end-before: end-kes-play-sandbox-warning

此流程需要以下组件：

- 在一台能够通过网络访问源部署的机器上安装 :mc:`mc`。
  有关下载和安装 ``mc`` 的说明，请参阅 ``mc`` :ref:`Installation Quickstart <mc-install>`。

- 在一台可访问互联网的机器上安装 :kes-docs:`MinIO Key Encryption Service (KES) <>`。
  有关下载、安装和配置 KES 的说明，请参阅 KES :kes-docs:`Getting Started <tutorials/getting-started/>` 指南。


1) 为 SSE-S3 加密创建加密密钥
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :kes-docs:`kes <cli>` 命令行工具创建一个新的 |EK|，供 SSE-S3 加密使用。

以下命令获取已连接到 KES ``play`` 沙箱的 KES 服务器的 root
:minio-git:`identity <kes/wiki/Configuration#policy-configuration>`：

.. code-block:: shell
   :class: copyable

   curl -sSL --tlsv1.2 \
     -O 'https://raw.githubusercontent.com/minio/kes/master/root.key' \
     -O 'https://raw.githubusercontent.com/minio/kes/master/root.cert'

在终端或 shell 中设置以下环境变量：

.. code-block:: shell
   :class: copyable

   export KES_CLIENT_KEY=root.key
   export KES_CLIENT_CERT=root.cert

.. list-table::
   :stub-columns: 1
   :widths: 40 60
   :width: 100%

   * - ``KES_CLIENT_KEY``
     - KES 服务器上某个 :kes-docs:`identity <concepts/#authorization>` 的私钥。
       该 identity 至少必须被授予对 ``/v1/create``、``/v1/generate`` 和
       ``/v1/list`` :kes-docs:`API endpoints <concepts/server-api/>` 的访问权限。
       此步骤使用 MinIO ``play`` KES 沙箱的 ``root`` identity，它可访问
       KES 服务器上的所有操作。

   * - ``KES_CLIENT_CERT``
     - KES 服务器上该 :kes-docs:`identity <concepts/#authorization>` 对应的证书。
       此步骤使用 MinIO ``play`` KES 沙箱的 ``root`` identity，它可访问
       KES 服务器上的所有操作。

以下命令通过 :kes-docs:`KES CLI <cli/kes-key/create/>` 创建一个新的 |EK|：

.. code-block:: shell
   :class: copyable

   kes key create my-minio-sse-s3-key

本教程使用示例名称 ``my-minio-sse-s3-key`` 以便引用。
请指定唯一的密钥名称，以避免与现有密钥冲突。

2) 配置 MinIO 以启用 SSE-S3 对象加密
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
      
在部署中每个 MinIO 服务器主机的 shell 或终端中设置以下环境变量：

.. code-block:: shell
   :class: copyable

   export MINIO_KMS_KES_ENDPOINT=https://play.min.io:7373
   export MINIO_KMS_KES_API_KEY=<API-key-identity-string-from-KES> # Replace with the key string for your credentials
   export MINIO_KMS_KES_KEY_NAME=my-minio-sse-s3-key

.. note::
   
   - API key 是与 KES 服务器进行身份验证的首选方式，因为它为 KES 服务器提供了
     更精简且安全的认证流程。

   - 或者，使用 :envvar:`MINIO_KMS_KES_KEY_FILE` 和
     :envvar:`MINIO_KMS_KES_CERT_FILE` 替代 :envvar:`MINIO_KMS_KES_API_KEY`。
     
     API key 与基于证书的身份验证互斥。
     请在 API key 变量与 Key File 和 Cert File 变量之间 *二选一*。
   
   - 本站文档使用 API key。

.. list-table::
   :stub-columns: 1
   :widths: 30 80

   * - :envvar:`MINIO_KMS_KES_ENDPOINT`
     - MinIO ``Play`` KES 服务的端点。

   * - :envvar:`MINIO_KMS_KES_KEY_FILE`
     - 与 KES 服务上的某个
       :minio-git:`identity <kes/wiki/Configuration#policy-configuration>`
       对应的私钥文件。该 identity 必须具备创建、生成和解密密钥的权限。
       请指定与上一步中 ``KES_KEY_FILE`` 环境变量相同的 identity 私钥文件。

   * - :envvar:`MINIO_KMS_KES_CERT_FILE`
     - 与 KES 服务上的某个
       :minio-git:`identity <kes/wiki/Configuration#policy-configuration>`
       对应的公钥证书文件。该 identity 必须具备创建、生成和解密密钥的权限。
       请指定与上一步中 ``KES_CERT_FILE`` 环境变量相同的 identity 证书文件。

   * - :envvar:`MINIO_KMS_KES_KEY_NAME`
     - 用于执行 SSE 加密操作的 |EK| 名称。
       KES 从已配置的 Key Management System (KMS) 中获取该 |EK|。
       请指定上一步创建的密钥名称。

3) 重启 MinIO 部署以启用 SSE-S3
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

必须重启 MinIO 部署以应用配置变更。
使用 :mc-cmd:`mc admin service restart` 命令重启部署。

.. code-block:: shell
   :class: copyable

   mc admin service restart ALIAS

将 ``ALIAS`` 替换为要重启的部署的 :ref:`alias <alias>`。

4) 配置存储桶自动加密
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

*可选*

如果你只打算使用客户端驱动的 SSE-S3，可以跳过此步骤。

使用 :mc:`mc encrypt set` 命令，为写入特定存储桶的所有对象启用自动
SSE-S3 保护。

.. code-block:: shell
   :class: copyable

   mc encrypt set sse-s3 ALIAS/BUCKET

- 将 :mc-cmd:`ALIAS <mc encrypt set ALIAS>` 替换为已启用 SSE-S3 的
  MinIO 部署的 :mc:`alias <mc alias>`。

- 将 :mc-cmd:`BUCKET <mc encrypt set ALIAS>` 替换为你要启用自动 SSE-S3 的
  存储桶或存储桶前缀的完整路径。

.. _minio-encryption-sse-s3-erasure-locking:

安全擦除与锁定
--------------------------

SSE-S3 使用服务器启动时通过 :envvar:`MINIO_KMS_KES_KEY_NAME` 环境变量指定的
|EK| 来保护对象。因此，MinIO *必须* 访问该 |EK| 才能解密该对象。

- 禁用该 |EK| 会使部署中经 SSE-S3 加密的对象暂时无法读取，从而被临时锁定。
  你之后可以重新启用该 |EK|，以恢复正常读取操作。

- 删除该 |EK| 会使部署中所有经 SSE-S3 加密的对象 *永久* 无法读取。
  如果 KMS 没有该 |EK| 的备份或不支持其备份，此过程 *不可逆*。

该 |EK| 的作用范围取决于：

- 哪些存储桶指定了自动 SSE-S3 加密，*以及*
- 哪些写入操作请求了 SSE-S3 加密。

.. _minio-encryption-sse-s3-encryption-process:

加密过程
------------------

.. note:: 

   以下部分描述 MinIO 的内部逻辑和功能。
   这些信息仅用于帮助理解，并非配置或实现任何 MinIO 功能所必需。

SSE-S3 使用由已配置的 Key Management System (KMS) 管理的 |EK| 来执行加密操作并
保护对象。下表描述了加密过程的各个阶段：

.. list-table::
   :header-rows: 1
   :widths: 30 70

   * - 阶段
     - 说明

   * - 启用 SSE 的写入操作
     - MinIO 接收到一个请求执行 SSE-S3 加密的写入操作。
       MinIO 将 :envvar:`MINIO_KMS_KES_KEY_NAME` 中指定的密钥名称用作 |EK|。

   * - 生成数据加密密钥（DEK）
     - .. include:: /includes/common-minio-sse.rst
          :start-after: start-sse-dek
          :end-before: end-sse-dek

   * - 生成密钥加密密钥（KEK）
     - .. include:: /includes/common-minio-sse.rst
          :start-after: start-sse-kek
          :end-before: end-sse-kek

   * - 生成对象加密密钥（OEK）
     - .. include:: /includes/common-minio-sse.rst
          :start-after: start-sse-oek
          :end-before: end-sse-oek

   * - 加密对象
     - MinIO 在将对象写入磁盘 *之前* 使用 |OEK| 对对象进行加密。
       随后，MinIO 使用 |KEK| 对 |OEK| 进行加密。

       MinIO 将 |OEK| 和 |DEK| 的加密表示形式作为元数据的一部分进行存储。
