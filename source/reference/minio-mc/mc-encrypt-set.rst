.. _minio-mc-encrypt-set:

==================
``mc encrypt set``
==================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc encrypt set

语法
----

.. start-mc-encrypt-set-desc

:mc:`mc encrypt set` 加密命令用于设置或更新存储桶默认的
:ref:`服务端加密（SSE）模式 <minio-sse>`。MinIO 会使用指定的 SSE 模式
自动加密写入该存储桶的对象。

.. end-mc-encrypt-set-desc

:mc:`mc encrypt set` 仅支持 :ref:`SSE-KMS <minio-encryption-sse-kms>`
和 :ref:`SSE-S3 <minio-encryption-sse-s3>`。

.. tab-set::

   .. tab-item:: 示例

      以下命令为 ``myminio`` MinIO 部署上的 ``mydata`` 存储桶设置默认的
      :ref:`SSE-KMS 加密密钥 <minio-encryption-sse-kms>`：

      .. code-block:: shell
         :class: copyable

         mc encrypt set sse-kms "minio-encryption-key" myminio/mydata

   .. tab-item:: 语法

      命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] encrypt set  ENCRYPTION [KMSKEY] ALIAS

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~

.. mc-cmd:: ENCRYPTION
   
      指定作为默认 SSE 模式的服务端加密类型。
      支持以下取值：

      - ``sse-kms`` - 使用 :mc-cmd:`~mc encrypt set KMSKEY` 中指定的密钥
        对对象加密。MinIO 必须能够访问外部 KMS 上指定的密钥，才能成功加密
        或解密受 SSE-KMS 保护的对象。

      - ``sse-s3`` - 使用 :envvar:`MINIO_KMS_KES_KEY_NAME` 指定的密钥
        对对象加密。MinIO 必须能够访问外部 KMS 上指定的密钥，才能成功加密
        或解密受 SSE-S3 保护的对象。

.. mc-cmd:: KMSKEY

   指定用于执行 SSE 对象加密的 KMS Master Key。该选项仅在
   :mc-cmd:`~mc encrypt set ENCRYPTION` 为 ``sse-kms`` 时生效。
   
   省略该选项可让 MinIO 使用
   :envvar:`MINIO_KMS_KES_KEY_NAME`.

.. mc-cmd:: ALIAS

   要设置默认 SSE 模式的存储桶完整路径。将 MinIO 部署的
   :ref:`alias <alias>` 作为 TARGET 路径前缀。例如：

   .. code-block:: shell

      mc encrypt set ENCRYPTION [KMSKEY] play/mybucket

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

启用自动服务端存储桶加密
~~~~~~~~~~~~~~~~~~~~~~~~

.. tab-set::

   .. tab-item:: 示例

      以下命令假设：
      
      - MinIO 服务端配置支持
        :ref:`SSE-KMS <minio-encryption-sse-kms>`

      - root KMS 上存在加密密钥 ``minio-encryption-key``。

      .. code-block:: shell
         :class: copyable

          mc encrypt set sse-kms minio-encryption-key myminio/data

   .. tab-item:: 语法

      .. code-block:: shell
         :class: copyable

         mc encrypt set ENCRYPTION KMSKEY TARGET

      - 将 ``ENCRYPTION`` 替换为 ``sse-kms`` 或 ``sse-s3``，具体取决于
        所需的加密模式。

      - 将 ``KMSKEY`` 替换为已配置 root KMS 中的加密密钥名称。
        该参数对 ``sse-s3`` 无效。

      - 将 ``TARGET`` 替换为要配置自动服务端存储桶加密的 MinIO 部署
        :ref:`alias <alias>`。

行为
----

:mc:`mc encrypt set` 不会对 MinIO 服务端当前的加密状态作任何假设。
如果指定了服务端无法支持的默认加密设置，可能导致非预期行为。

设置或修改默认服务端加密设置时，*不会* 自动加密或解密存储桶中已有内容。
如果存储桶内容 *必须* 保持一致的加密状态，请使用 :mc:`mc mv` 命令并结合
:mc-cmd:`~mc mv --enc-kms`、:mc-cmd:`~mc mv --enc-s3` 或
:mc-cmd:`~mc mv --enc-c` 指定迁移内容使用的加密类型。
这会在更改存储桶默认设置 *之前*，手动修改存储桶内容的加密设置或加密状态。
