部署启用服务器端加密的 MinIO 和 KES
---------------------------------

开始以下步骤前，请先创建这些目录：

.. code-block:: shell
   :class: copyable
   :substitutions:

   mkdir -p |kescertpath|
   mkdir -p |kesconfigpath|
   mkdir -p |miniodatapath|

对于 Windows 主机，请将路径替换为 Windows 风格路径，例如 ``C:\minio-kes-vault\``。


前提条件
~~~~~~~~

根据你选择的 :kes-docs:`受支持 KMS 目标 <#supported-kms-targets>` 配置，
你可能需要将 ``kes-server.cert`` 作为受信任的 Certificate Authority (CA) 传递。
关于如何信任第三方 CA，请参阅客户端文档。

1) 创建 KES 和 MinIO 配置
~~~~~~~~~~~~~~~~~~~~~~~~~~

a. 创建 KES 配置文件

   使用你偏好的文本编辑器创建配置文件。
   以下示例使用 ``nano``：

   .. code-block:: shell
      :substitutions:

      nano |kesconfigpath|/kes-config.yaml

   .. include:: /includes/common/common-minio-kes-hashicorp.rst
      :start-after: start-kes-configuration-hashicorp-vault-desc
      :end-before: end-kes-configuration-hashicorp-vault-desc

   - 将 ``MINIO_IDENTITY_HASH`` 设置为 MinIO mTLS 证书的 identity hash。

      以下命令会计算所需的 hash：

      .. code-block:: shell
         :class: copyable
         :substitutions:

         podman run --rm                                             \
            -v |kescertpath|/certs:/certs                                \
            kes:|kes-stable| tool identity of /certs/minio-kes.cert

   - 请参阅你所选 :kes-docs:`受支持 KMS 方案 <#kes-supported-targets>` 的 KES 配置说明，
     了解还需为目标 KMS 定义哪些额外变量。

b. 创建 MinIO 环境文件

   使用你偏好的文本编辑器创建环境文件。
   以下示例使用 ``nano``：

   .. code-block:: shell
      :substitutions:

      nano |minioconfigpath|/minio

   .. include:: /includes/container/common-minio-kes.rst
      :start-after: start-kes-configuration-minio-desc
      :end-before: end-kes-configuration-minio-desc

2) 创建 Pod 和容器
~~~~~~~~~~~~~~~~~~

.. include:: /includes/container/common-minio-kes.rst
   :start-after: start-common-deploy-create-pod-and-containers
   :end-before: end-common-deploy-create-pod-and-containers

3) 生成新的加密密钥
~~~~~~~~~~~~~~~~~~

.. include:: /includes/container/common-minio-kes.rst
   :start-after: start-kes-generate-key-desc
   :end-before: end-kes-generate-key-desc

4) 为存储桶启用 SSE-KMS
~~~~~~~~~~~~~~~~~~~~~~

使用 MinIO :mc:`mc` CLI，通过刚生成的密钥启用存储桶默认 SSE-KMS：


以下命令会：

- 为该 MinIO 部署创建新的 :ref:`alias <alias>`
- 创建一个用于存储加密数据的新存储桶
- 在该存储桶上启用 SSE-KMS 加密

.. code-block:: shell
   :class: copyable

   mc alias set local http://127.0.0.1:9000 ROOTUSER ROOTPASSWORD

   mc mb local/encryptedbucket
   mc encrypt set SSE-KMS encrypted-bucket-key ALIAS/encryptedbucket

使用 :mc:`mc cp` 或任意带有 ``PutObject`` 函数的 S3 兼容 SDK 向该存储桶写入文件。
然后，你可以对该文件运行 :mc:`mc stat`，以确认关联的加密元数据。
