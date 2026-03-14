.. _minio-server-envvar-kes:

===============================
密钥加密服务设置
===============================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. |SSE| replace:: :abbr:`SSE (服务端加密)`

MinIO Server 提供三组环境变量，用于管理 MinIO Server 与 Key Encryption Service (KES)、Key Management Service (KMS) 或静态密钥文件的交互方式。
这三组中只能定义一组。
如果定义了多种类型的环境变量组，MinIO 会返回错误。

.. note::

   这些设置不支持通过 :mc:`mc admin config set` 进行配置。

在启动或重启 MinIO 进程之前，请在宿主系统中定义其中任意一组环境变量。
有关如何定义环境变量，请参考操作系统文档。

.. include:: /includes/common-mc-admin-config.rst
   :start-after: start-minio-settings-test-before-prod
   :end-before: end-minio-settings-test-before-prod

Key Encryption Service
----------------------

定义以下变量以使用 Key Encryption Service (KES) 连接到 :kes-docs:`受支持的第三方 Key Management Service 提供商 <#supported-kms-targets>`。

.. envvar:: MINIO_KMS_KES_ENDPOINT

   MinIO Key Encryption Service (KES) 进程用于支持 SSE-S3 和 MinIO 后端加密操作的 endpoint。
   多个 KES endpoint 使用 ``,`` 分隔。

.. envvar:: MINIO_KMS_KES_KEY_NAME

   KES 服务器上配置的 Key Management system (KMS) 中外部密钥名称，用于执行加密和解密操作。
   MinIO 将此密钥用于以下用途：

   - 加密后端数据（:ref:`IAM <minio-authentication-and-identity-management>`、服务器配置）。

   - :ref:`SSE-KMS <minio-encryption-sse-kms>` 的默认服务端加密密钥。

   - :ref:`SSE-S3 <minio-encryption-sse-s3>` 的服务端加密密钥。

   .. important::

      .. include:: /includes/common/common-minio-kes.rst
         :start-after: start-kes-encrypted-backend-desc
         :end-before: end-kes-encrypted-backend-desc

.. envvar:: MINIO_KMS_KES_API_KEY

   使用通过 :kes-docs:`kes identity new <cli/kes-identity/new/>` 命令获取的 KES API key 与加密服务进行身份认证的首选方式。

   此环境变量与 :envvar:`MINIO_KMS_KES_KEY_FILE` 和 :envvar:`MINIO_KMS_KES_CERT_FILE` 环境变量互斥。

.. envvar:: MINIO_KMS_KES_KEY_FILE

   与 :envvar:`MINIO_KMS_KES_CERT_FILE` x.509 证书关联的私钥，用于向 KES 服务器进行身份认证。
   KES 服务器要求客户端提供证书以执行 mutual TLS (mTLS)。

   有关 KES 访问控制的完整文档，请参见 :minio-git:`KES wiki <kes/wiki/Configuration#policy-configuration>`。

   还必须设置 :envvar:`MINIO_KMS_KES_CERT_FILE`。
   此变量与 :envvar:`MINIO_KMS_KES_API_KEY` 互斥。

.. envvar:: MINIO_KMS_KES_CERT_FILE

   提供给 KES 服务器的 x.509 证书。
   KES 服务器要求客户端提供证书以执行 mutual TLS (mTLS)。

   KES 服务器会根据证书计算 :minio-git:`identity <kes/wiki/Configuration#policy-configuration>`，并将其与已配置策略进行比对。
   KES 服务器仅向 :mc:`minio` 服务器授予策略中明确允许的操作访问权限。

   有关 KES 访问控制的完整文档，请参见 :minio-git:`KES wiki <kes/wiki/Configuration#policy-configuration>`。

   还必须设置 :envvar:`MINIO_KMS_KES_KEY_FILE`。
   此变量与 :envvar:`MINIO_KMS_KES_API_KEY` 互斥。

.. envvar:: MINIO_KMS_KES_CAPATH
   :optional:

   允许使用自签名或第三方 :abbr:`CA (Certificate Authority)` 验证 KES 服务器证书。
   指定 KES 部署所使用的 :abbr:`CA (Certificate Authority)` 证书路径。

   如果使用公共证书颁发机构，则不需要此变量。

.. envvar:: MINIO_KMS_KES_KEY_PASSWORD
   :optional:

   用于加密和解密 TLS 私钥的密码（如果使用）。

MinIO Key Management Server (KMS)
---------------------------------

定义以下变量以使用 `MinIO KMS <https://min.io/product/enterprise/key-management-server?ref=docs>`__ 管理密钥。

.. envvar:: MINIO_KMS_SERVER

   MinIO Key Management Service (KMS) 进程用于支持 SSE-S3 和 MinIO 后端加密操作的 endpoint。
   多个 KMS endpoint 使用 ``,`` 分隔。

.. envvar:: MINIO_KMS_ENCLAVE

   密钥和身份所在的 MinIO KMS Enclave。

.. envvar:: MINIO_KMS_SSE_KEY

   当调用未指定密钥身份时，用于 SSE-S3 加密的默认密钥。

.. envvar:: MINIO_KMS_API_KEY

   用于向 MinIO KMS 服务进行身份认证的凭据。

静态密钥文件
------------

.. warning::

   这些设置用于在不依赖外部 KMS 的情况下，对对象服务端加密进行早期开发和评估。
   不要在长期开发、QA 或生产环境中使用这些设置。
   关于如何使用 MinIO Key Encryption Service (KES) 和外部 KMS 部署 SSE，请参见 :ref:`minio-sse-vault`。

提供静态 KMS 密钥或密钥文件用于加密。

.. envvar:: MINIO_KMS_SECRET_KEY

   静态 KMS 密钥的 base64 形式，格式为 ``<key-name>:<base64-32byte-key>``。
   实现了部分 KMS API。

.. envvar:: MINIO_KMS_SECRET_KEY_FILE

   读取静态 KMS 密钥的文件路径。
