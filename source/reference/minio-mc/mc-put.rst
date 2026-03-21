==========
``mc put``
==========

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc put

.. versionadded:: mc RELEASE.2024-02-24T01-33-20Z

语法
----

.. start-mc-put-desc

:mc:`mc put` 将对象从本地文件系统上传到目标 S3 部署中的存储桶。

.. end-mc-put-desc

与 :mc:`mc cp` 或 :mc:`mc mirror` 相比，``mc put`` 为文件上传提供了更简化的接口。
``mc put`` 使用单向上传机制，以牺牲效率为代价换取相较其他命令更低的复杂度。


.. tab-set::

   .. tab-item:: 示例

      以下示例将本地文件系统路径 ``~/images/collateral/`` 下的文件 ``logo.png`` 上传到别名为 ``minio`` 的 MinIO 部署中名为 ``marketing`` 的存储桶。

      .. code-block:: shell
         :class: copyable

         mc put ~/images/collateral/logo.png minio/marketing


   .. tab-item:: 语法

      该命令的语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] put                            \
                          TARGET                         \
                          [--checksum value]             \
                          [--disable-multipart]          \
                          [--enc-kms value]              \
                          [--enc-s3 value]               \
                          [--enc-c value]                \
                          [--if-not-exists]              \
                          [--parallel, -P integer]       \
                          [--part-size, -s string]       \
                          [--storage-class, -sc string]

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~

.. mc-cmd:: TARGET
   :required:

   命令应执行位置的 :ref:`alias <minio-mc-alias>` 或前缀的完整路径。
   TARGET *必须* 包含 :ref:`alias <alias>` 和 ``bucket`` 名称。

   TARGET 还可以包含以下可选组成部分：
   - 对象应上传到的 PREFIX
   - 用于替代文件名的 OBJECT-NAME

   有效的 TARGET 可以采用以下任一形式：
   - ``ALIAS/BUCKET``
   - ``ALIAS/BUCKET/PREFIX``
   - ``ALIAS/BUCKET/OBJECT-NAME``
   - ``ALIAS/BUCKET/PREFIX/OBJECT-NAME``

.. mc-cmd:: --checksum
   :optional:

   .. versionadded:: RELEASE.2024-10-02T08-27-28Z

   为上传对象添加校验和。

   可选值包括：
   - ``MD5``
   - ``CRC32``
   - ``CRC32C``
   - ``SHA1``
   - ``SHA256``

   该标志需要服务器支持 trailing headers，并可用于 AWS 或 MinIO 目标。

.. mc-cmd:: --disable-multipart
   :optional:

   .. versionadded:: RELEASE.2024-10-02T08-27-28Z

   禁用分段上传，并指示 ``mc`` 通过单次 ``PUT`` 操作发送对象。

.. block include of enc-c , enc-s3, and enc-kms

.. include:: /includes/common-minio-sse.rst
   :start-after: start-minio-mc-sse-options
   :end-before: end-minio-mc-sse-options


.. mc-cmd:: --parallel, --P
   :optional:

   对于分段上传，指定对象分段并行上传的数量。

   如未定义，默认值为 ``4``。

.. mc-cmd:: --part-size, -s
   :optional:

   指定分段上传中每个分段使用的大小。

   如未定义，默认值为 ``16MiB``。

.. mc-cmd:: --storage-class, -sc
   :optional:

   为上传对象设置存储类。

   有关存储类的更多信息，请参见 :ref:`Standard Storage Class <minio-ec-storage-class-standard>`。


全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

上传文件并指定对象名称
~~~~~~~~~~~~~~~~~~~~~~

以下命令将本地文件系统中的文件 ``logo.png`` 上传到 ``minio`` 部署的 ``business`` 存储桶，并在目标端命名为 ``company-logo.png``。

.. code-block:: shell
   :class: copyable

   mc put images/collateral/logo.png minio/business/company-logo.png

按指定分段大小并行上传分段对象
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下命令以每段 20MiB 的分块方式上传文件，并行上传其中 8 个分段。
系统会持续按批次上传 8 个分段，直到对象的所有分段均上传完成。

.. code-block:: shell
   :class: copyable

   mc put ~/videos/collateral/splash-page.mp4 minio/business --parallel 8 --part-size 20MiB
