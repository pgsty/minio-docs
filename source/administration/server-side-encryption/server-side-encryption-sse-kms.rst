.. _minio-encryption-sse-kms:

================================================================
使用按存储桶划分密钥的服务端加密（SSE-KMS）
================================================================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 1

.. |EK|  replace:: :abbr:`EK (外部密钥)`
.. |DEK| replace:: :abbr:`DEK (数据加密密钥)`
.. |KEK| replace:: :abbr:`KEK (密钥加密密钥)`
.. |OEK| replace:: :abbr:`OEK (对象加密密钥)`
.. |SSE| replace:: :abbr:`SSE (服务端加密)`
.. |KMS| replace:: :abbr:`KMS (密钥管理服务)`
.. |KES| replace:: :abbr:`KES (Key Encryption Service)`

MinIO 服务端加密（SSE）在写入操作过程中保护对象，使客户端能够利用服务端的处理能力在存储层保护对象（静态加密）。
SSE 还为围绕安全锁定和擦除的监管与合规要求提供关键能力。

MinIO SSE 使用 :kes-docs:`MinIO Key Encryption Service (KES) <>` 和受支持的
:kes-docs:`外部密钥管理服务（KMS） <#supported-kms-targets>`，以安全地大规模执行加密操作。
MinIO 还支持由客户端管理密钥的模式，此时应用程序对为 MinIO SSE 创建和管理加密密钥承担全部责任。

MinIO SSE-KMS 使用由密钥管理系统（KMS）管理的外部密钥（EK）对对象进行加密或解密。
每个存储桶和对象都可以拥有单独的 |EK|，从而在部署中支持更细粒度的加密操作。
只有在 MinIO 同时能够访问 KMS *以及* 用于加密该对象的 |EK| 时，才能解密该对象。

你可以使用 :mc:`mc encrypt set` 命令启用存储桶默认的 SSE-KMS 加密：

.. code-block:: shell
   :class: copyable

   mc encrypt set sse-kms EXTERNALKEY play/mybucket

- 将 ``EXTERNALKEY`` 替换为用于加密该存储桶中对象的 |EK| 名称。

- 将 ``play/mybucket`` 替换为你要启用自动 SSE-KMS 加密的
  :mc:`alias <mc alias>` 和存储桶。

MinIO SSE-KMS 在功能上与 AWS S3
:s3-docs:`使用存储在 AWS 中的 KMS 密钥进行服务端加密 <UsingKMSEncryption.html>`
兼容，同时将支持扩展到以下 KMS 提供商：

- :kes-docs:`AWS Secrets Manager <integrations/aws-secrets-manager/>`
- :kes-docs:`Azure Key Vault <integrations/azure-keyvault/>`
- :kes-docs:`Entrust KeyControl <integrations/entrust-keycontrol/>`
- :kes-docs:`Fortanix SDKMS <integrations/fortanix-sdkms/>`
- :kes-docs:`Google Cloud Secret Manager <integrations/google-cloud-secret-manager/>`
- :kes-docs:`HashiCorp Vault Keystore <integrations/hashicorp-vault-keystore/>`
- :kes-docs:`Thales CipherTrust Manager (formerly Gemalto KeySecure) <integrations/thales-ciphertrust/>`

.. _minio-encryption-sse-kms-quickstart:

快速开始
--------

.. important::

   .. include:: /includes/common/common-minio-kes.rst
      :start-after: start-kes-encrypted-backend-desc
      :end-before: end-kes-encrypted-backend-desc

以下过程使用 ``play`` MinIO |KES| 沙箱，在评估和早期开发环境中为 |SSE|
提供 SSE-KMS 支持。

对于扩展开发环境或生产环境，请使用以下受支持的外部密钥管理服务（KMS）之一：

- :kes-docs:`AWS Secrets Manager <integrations/aws-secrets-manager/>`
- :kes-docs:`Azure Key Vault <integrations/azure-keyvault/>`
- :kes-docs:`Entrust KeyControl <integrations/entrust-keycontrol/>`
- :kes-docs:`Fortanix SDKMS <integrations/fortanix-sdkms/>`
- :kes-docs:`Google Cloud Secret Manager <integrations/google-cloud-secret-manager/>`
- :kes-docs:`HashiCorp Vault Keystore <integrations/hashicorp-vault-keystore/>`
- :kes-docs:`Thales CipherTrust Manager (formerly Gemalto KeySecure) <integrations/thales-ciphertrust/>`

.. include:: /includes/common/common-minio-kes.rst
   :start-after: start-kes-play-sandbox-warning
   :end-before: end-kes-play-sandbox-warning

此过程需要以下组件：

- 在一台能够通过网络访问源部署的机器上安装 :mc:`mc`。
  有关下载和安装 ``mc`` 的说明，请参阅 ``mc`` :ref:`安装快速开始 <mc-install>`。


- 在一台可以访问互联网的机器上安装 :kes-docs:`MinIO Key Encryption Service (KES) <>`。
  有关下载、安装和配置 KES 的说明，请参阅 ``kes`` :kes-docs:`快速开始 <tutorials/getting-started/>` 指南。

1) 为 SSE-KMS 加密创建加密密钥
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :kes-docs:`kes <cli/>` 命令行工具创建一个新的外部密钥（EK），供 SSE-KMS 加密使用。

以下命令获取 ``play`` KES 服务器的 root :kes-docs:`身份 <concepts/#authorization>`：

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
     - KES 服务器上某个 :kes-docs:`身份 <concepts/#authorization>` 的私钥。
       该身份至少必须被授予对 ``/v1/create``、``/v1/generate`` 和 ``/v1/list`` :kes-docs:`API 端点 <concepts/server-api/>` 的访问权限。
       本步骤使用 MinIO ``play`` KES 沙箱中的 ``root`` 身份，该身份可访问 KES 服务器上的所有操作。

   * - ``KES_CLIENT_CERT``
     - KES 服务器上该 :kes-docs:`身份 <concepts/#authorization>` 对应的证书。
       本步骤使用 MinIO ``play`` KES 沙箱中的 ``root`` 身份，该身份可访问 KES 服务器上的所有操作。

以下命令通过 KES 创建一个新的 |EK|。

.. code-block:: shell
   :class: copyable

   kes key create my-minio-sse-kms-key

本教程使用示例名称 ``my-minio-sse-kms-key`` 以便引用。
请指定唯一的密钥名称，以避免与现有密钥冲突。

2) 配置 MinIO 以进行 SSE-KMS 对象加密
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
      
在部署中的每台 MinIO 服务器主机上，于 shell 或终端中指定以下环境变量：

.. code-block:: shell
   :class: copyable

   export MINIO_KMS_KES_ENDPOINT=https://play.min.io:7373
   export MINIO_KMS_KES_API_KEY=<API-key-identity-string-from-KES> # Replace with the key string for your credentials
   export MINIO_KMS_KES_KEY_NAME=my-minio-sse-s3-key

.. note::
   
   - API 密钥是与 KES 服务器进行身份验证的首选方式，因为它提供了更简洁且更安全的认证流程。

   - 或者，也可以指定 :envvar:`MINIO_KMS_KES_KEY_FILE` 和 :envvar:`MINIO_KMS_KES_CERT_FILE`，而不是 :envvar:`MINIO_KMS_KES_API_KEY`。
     
     API 密钥与基于证书的身份验证互斥。
     请指定 API 密钥变量，*或* 指定密钥文件和证书文件变量。
   
   - 本站文档使用 API 密钥。

.. list-table::
   :stub-columns: 1
   :widths: 30 80

   * - :envvar:`MINIO_KMS_KES_ENDPOINT`
     - MinIO ``Play`` KES 服务的端点。

   * - :envvar:`MINIO_KMS_KES_API_KEY`
     - KES 为 MinIO 部署 :kes-docs:`生成的 <tutorials/kes-for-minio/#kes-server-setup>` API 密钥。
       该 API 密钥对应的身份必须具有创建、生成和解密密钥的权限。

       API 密钥是与 KES 服务器进行身份验证的首选方式。
       如果情况需要，请改为指定 :envvar:`MINIO_KMS_KES_KEY_FILE` 和 :envvar:`MINIO_KMS_KES_CERT_FILE`。
       请指定 API 密钥，*或* 指定密钥文件和证书文件。
       *不要* 同时填充这三个环境变量。

   * - :envvar:`MINIO_KMS_KES_KEY_NAME`
     - 用于执行 SSE 加密操作的外部密钥（EK）名称。
       KES 会从已配置的密钥管理服务（KMS）中检索该 |EK|。
       指定上一步创建的密钥名称。


3) 重启 MinIO 部署以启用 SSE-KMS
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

你必须重启 MinIO 部署以应用配置变更。
使用 :mc-cmd:`mc admin service restart` 命令重启该部署。

.. code-block:: shell
   :class: copyable

   mc admin service restart ALIAS

将 ``ALIAS`` 替换为要重启的部署的 :ref:`alias <alias>`。

4) 配置自动存储桶加密
~~~~~~~~~~~~~~~~~~~~~

使用 :mc:`mc encrypt set` 命令，为写入特定存储桶的所有对象启用自动 SSE-KMS 保护。

.. code-block:: shell
   :class: copyable

   mc encrypt set sse-kms my-minio-sse-kms-key ALIAS/BUCKET

- 将 :mc-cmd:`ALIAS <mc encrypt set ALIAS>` 替换为已启用 SSE-KMS 的
  MinIO 部署的 :mc:`alias <mc alias>`。

- 将 :mc-cmd:`BUCKET <mc encrypt set ALIAS>` 替换为你要启用自动 SSE-KMS 的
  存储桶或存储桶前缀的完整路径。

写入指定存储桶的对象会自动使用指定的 |EK| 加密。

对于每个要启用自动 SSE-KMS 加密的存储桶，请重复此步骤。
你可以按存储桶或存储桶前缀生成额外的密钥，从而将每个 |EK| 的作用范围限制为对象子集。


.. _minio-encryption-sse-kms-erasure-locking:

安全擦除与锁定
--------------

SSE-KMS 使用在存储桶自动加密设置中指定的 |EK|，或在写入操作中指定的 |EK| 来保护对象。
因此，MinIO 在解密该对象时 *必须* 能够访问该 |EK|。

- 禁用 |EK| 会暂时锁定使用该 |EK| 加密的对象，使其变得不可读。
  之后你可以重新启用该 |EK|，以恢复这些对象的正常读取操作。

- 删除 |EK| 会使所有由该 |EK| 加密的对象 *永久* 不可读。
  如果 KMS 没有该 |EK| 的备份或不支持其备份，则此过程 *不可逆*。

单个 |EK| 的作用范围取决于：

- 哪些存储桶将该 |EK| 指定为自动 SSE-KMS 加密所用密钥，
  *以及*
- 哪些写入操作在请求 SSE-KMS 加密时指定了该 |EK|。

例如，假设一个 MinIO 部署为每个存储桶使用一个 |EK|。
禁用其中一个 |EK| 会使关联存储桶中的所有对象不可读，而不会影响其他存储桶。
如果该部署改为对所有对象和存储桶使用同一个 |EK|，则禁用该 |EK| 会使部署中的所有对象都不可读。

.. _minio-encryption-sse-kms-encryption-process:

加密过程
--------

.. note:: 

   本节介绍 MinIO 的内部逻辑和功能。
   这些信息仅用于帮助理解，并不是配置或实现任何 MinIO 功能的前提条件。

SSE-KMS 使用由已配置密钥管理系统（KMS）管理的外部密钥（EK）
来执行加密操作并保护对象。下表描述了加密过程的各个阶段：

.. list-table::
   :header-rows: 1
   :widths: 30 70

   * - 阶段
     - 说明

   * - 启用 SSE 的写入操作
     - MinIO 接收到一个请求使用 SSE-KMS 加密的写入操作。
       该写入操作 *必须* 关联一个用于加密对象的外部密钥（EK）。

       - 对于位于已启用自动 SSE-KMS 的存储桶中的写入操作，
         MinIO 使用该存储桶的 |EK|。如果写入操作包含显式指定的 EK，
         MinIO 会使用它来 *替代* 存储桶 EK。

       - 对于位于 *未* 启用自动 SSE-KMS 的存储桶中的写入操作，
         MinIO 使用该写入操作指定的 |EK|。

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
     - MinIO 在将对象写入驱动器 *之前* 使用 |OEK| 对对象进行加密。
       然后，MinIO 再使用 |KEK| 对 |OEK| 进行加密。

       MinIO 将 |OEK| 和 |DEK| 的加密表示作为元数据的一部分存储。

对于读取操作，MinIO 会先获取 |EK| 以解密 |DEK|。
随后 MinIO 会重新生成 |KEK|、解密 |OEK|，并解密该对象。
