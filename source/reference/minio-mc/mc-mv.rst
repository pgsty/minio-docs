=========
``mc mv``
=========

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc mv

语法
------

.. start-mc-mv-desc

:mc:`mc mv` 命令将对象从源移动到目标，例如在不同 MinIO 部署之间移动，*或* 在同一 MinIO 部署的不同存储桶之间移动。
:mc:`mc mv` 还支持在本地文件系统与 MinIO 之间移动对象。

.. end-mc-mv-desc

你也可以对本地文件系统使用 :mc:`mc mv`，以获得与 ``mv`` 命令行工具类似的结果。

.. tab-set::

   .. tab-item:: 示例

      以下命令将对象从 ``mydata`` 存储桶移动到 ``myminio`` MinIO 部署上的 ``archive`` 存储桶：

      .. code-block:: shell
         :class: copyable

         mc mv --recursive myminio/mydata myminio/archive

   .. tab-item:: 语法

      该命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] mv         \
         [--attr "string"]           \
         [--disable-multipart]       \
         [--enc-kms "string"]        \
         [--enc-s3 "string"]         \
         [--enc-c "string"]          \
         [--limit-download string]   \
         [--limit-upload string]     \
         [--newer-than "string"]     \
         [--older-than "string"]     \
         [--preserve]                \
         [--recursive]               \
         [--storage-class "string"]  \
         SOURCE [SOURCE...]          \
         TARGET

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~~~~~~~

.. mc-cmd:: SOURCE
   :required:

  要移动的一个或多个对象。

   若要从 MinIO 存储桶移动对象，请指定 :ref:`alias <alias>`
   以及对象的完整路径（例如存储桶和对象路径）。例如：

   .. code-block:: shell

      mc mv play/mybucket/object.txt play/myotherbucket/object.txt

   若要从本地文件系统移动对象，请指定该对象的完整路径。例如：

   .. code-block:: shell

      mc mv ~/mydata/object.txt play/mybucket/object.txt

   指定多个 ``SOURCE`` 路径可将多个对象移动到指定的
   :mc-cmd:`~mc mv TARGET`。:mc:`mc rm` 将*最后一个*指定的 alias 或文件系统路径
   视为 ``TARGET``。例如：

   .. code-block:: shell

      mc mv ~/mydata/object.txt play/mydata/otherobject.txt myminio/mydata

   如果你为 :mc-cmd:`~mc mv SOURCE` 指定的是目录或存储桶，
   则还必须指定 :mc-cmd:`~mc mv --recursive` 以递归移动该目录内容。
   如果省略 :mc-cmd:`~mc mv --recursive` 参数，:mc:`~mc mv` 仅会移动
   指定目录或存储桶顶层中的对象。

.. mc-cmd:: TARGET
   :required:

   命令会将指定 :mc-cmd:`~mc mv SOURCE` 的对象移动到该存储桶，
   这里填写该存储桶的完整路径。请将已配置 S3 服务的
   :ref:`alias <alias>` 作为 :mc-cmd:`~mc mv TARGET` 路径前缀。

   若要从 MinIO 移动对象，请指定 :ref:`alias <alias>`
   以及对象的完整路径（例如存储桶和对象路径）。例如：

   .. code-block:: shell

      mc mv play/mybucket/object.txt play/myotherbucket/object.txt

   若要从本地文件系统移动对象，请指定该对象的完整路径。例如：

   .. code-block:: shell

      mc mv ~/mydata/object.txt play/mybucket/object.txt

   ``TARGET`` 对象名可以不同于 ``SOURCE``，
   以便在移动操作中同时“重命名”对象。

   如果以 :mc-cmd:`~mc mv --recursive` 选项运行 :mc:`mc mv`，
   :mc:`mc mv` 会将 ``TARGET`` 视为 ``SOURCE`` 下所有对象的存储桶前缀。

.. mc-cmd:: --attr
   :optional:

   为对象添加自定义元数据。将键值对指定为 ``KEY=VALUE\;``。
   例如，``--attr key1=value1\;key2=value2\;key3=value3``。

.. mc-cmd:: --disable-multipart
   :optional:

   禁用 multipart upload 功能。

   Multipart upload 会将对象拆分为多个独立分片。
   每个分片可独立上传，且顺序不限。
   如果任一分片上传失败，MinIO 会仅重试该分片而不影响其他分片。
   上传完成后，这些分片会合并还原为原始对象。

   MinIO 建议对大于 100 MB 的对象使用 multipart upload。
   有关 multipart upload 的更多信息，请参阅 :s3-docs:`Amazon S3 documentation <mpuoverview.html>`

.. block include of enc-c , enc-s3, and enc-kms

.. include:: /includes/common-minio-sse.rst
   :start-after: start-minio-mc-sse-options
   :end-before: end-minio-mc-sse-options


.. include:: /includes/linux/minio-client.rst
   :start-after: start-mc-limit-flags-desc
   :end-before: end-mc-limit-flags-desc

.. mc-cmd:: --newer-than
   :optional:

   删除比指定天数更新的对象。指定格式为 ``##d#hh#mm#ss``
   的字符串。例如：
   ``--newer-than 1d2hh3mm4ss``。

   默认为 ``0``（所有对象）。

.. mc-cmd:: --older-than
   :optional:

   删除早于指定时间限制的对象。指定格式为 ``#d#hh#mm#ss``
   的字符串。例如：``--older-than 1d2hh3mm4ss``。

   默认为 ``0``（所有对象）。

.. mc-cmd:: --preserve, a
   :optional:

   保留 :mc-cmd:`~mc mv SOURCE` 目录、存储桶和对象在文件系统上的属性
   以及存储桶策略规则，并应用到 :mc-cmd:`~mc mv TARGET` 存储桶。

.. mc-cmd:: --recursive, r
   :optional:

   将每个 :mc-cmd:`~mc mv SOURCE` 存储桶或目录的内容
   递归移动到 :mc-cmd:`~mc mv TARGET` 存储桶。

.. mc-cmd:: --storage-class
   :optional:

   为 :mc-cmd:`~mc mv TARGET` 上的新对象设置存储类别。

   有关 S3 storage class 的更多信息，请参阅 Amazon 文档
   :aws-docs:`Storage Classes <AmazonS3/latest/dev/storage-class-intro.html>`。

全局标志
~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals


示例
--------

将文件从文件系统移动到 S3-Compatible Host
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: shell
   :class: copyable

   mc mv [--recursive] FILEPATH ALIAS/PATH

- 将 :mc-cmd:`FILEPATH <mc mv SOURCE>` 替换为要移动文件的完整文件路径。

  如果指定的是目录路径，请包含
  :mc-cmd:`~mc mv --recursive` 标志。

  :mc:`mc mv` 在成功移动到目标后，会从源中*移除*这些文件。

- 将 :mc-cmd:`ALIAS <mc mv TARGET>` 替换为已配置 S3-compatible host 的
  :mc-cmd:`alias <mc alias>`。

- 将 :mc-cmd:`PATH <mc mv TARGET>` 替换为目标存储桶。

将文件从文件系统移动到 S3-Compatible Host 并附带自定义元数据
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

使用带 :mc-cmd:`~mc mv --attr` 选项的 :mc:`mc mv`，
可为文件设置自定义属性。

.. code-block:: shell
   :class: copyable

   mc mv --attr "ATTRIBUTES" FILEPATH ALIAS/PATH

- 将 :mc-cmd:`FILEPATH <mc mv SOURCE>` 替换为要移动文件的完整文件路径。
  :mc:`mc mv` 在成功移动到目标后，会从源中*移除*该文件。

- 将 :mc-cmd:`ALIAS <mc mv TARGET>` 替换为已配置 S3-compatible host 的
  :mc-cmd:`alias <mc alias>`。

- 将 :mc-cmd:`PATH <mc mv TARGET>` 替换为目标存储桶。

- 将 :mc-cmd:`ATTRIBUTES <mc mv --attr>` 替换为一个或多个逗号分隔的
  键值对 ``KEY=VALUE``。每个键值对表示一个属性键及其值。

在 S3-Compatible 服务之间移动存储桶
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: shell
   :class: copyable

    mc mv --recursive SRCALIAS/SRCPATH TGTALIAS/TGTPATH

- 将 :mc-cmd:`SRCALIAS <mc mv SOURCE>` 替换为已配置 S3-compatible host 的
  :mc-cmd:`alias <mc alias>`。

- 将 :mc-cmd:`SRCPATH <mc mv SOURCE>` 替换为存储桶路径。
  :mc:`mc mv` 在成功移动到目标后，会从源中*移除*该存储桶及其内容。

- 将 :mc-cmd:`TGTALIAS <mc mv TARGET>` 替换为已配置 S3-compatible host 的
  :mc-cmd:`alias <mc alias>`。

- 将 :mc-cmd:`TGTPATH <mc mv TARGET>` 替换为存储桶路径。


将文件移动到 S3-Compatible Host 并指定存储类别
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

使用带 :mc-cmd:`~mc mv --storage-class` 选项的 :mc:`mc mv`，
可在目标 S3-compatible host 上设置存储类别。

.. code-block:: shell
   :class: copyable

   mc mv --storage-class CLASS FILEPATH ALIAS/PATH

- 将 :mc-cmd:`CLASS <mc mv --storage-class>` 替换为要
  关联到文件的存储类别。

- 将 :mc-cmd:`FILEPATH <mc mv SOURCE>` 替换为要移动文件的完整文件路径。
  :mc:`mc mv` 在成功移动到目标后，会从源中*移除*该文件。

- 将 :mc-cmd:`ALIAS <mc mv TARGET>` 替换为已配置 S3-compatible host 的
  :mc-cmd:`alias <mc alias>`。

- 将 :mc-cmd:`PATH <mc mv TARGET>` 替换为目标存储桶。

- 将 :mc-cmd:`ATTRIBUTES <mc mv --attr>` 替换为一个或多个逗号分隔的
  键值对 ``KEY=VALUE``。每个键值对表示一个属性键及其值。

   mc mv --storage-class REDUCED_REDUNDANCY myobject.txt play/mybucket


行为
--------

移动时的对象名称
~~~~~~~~~~~~~~~~~~~~

如果未显式指定目标对象名，在将对象移动到
:mc-cmd:`~mc mv TARGET` 时，MinIO 会使用
:mc-cmd:`~mc mv SOURCE` 的对象名。

你可以在相同对象路径下，为 :mc-cmd:`~mc mv TARGET`
指定不同的对象名，以在移动时“重命名”对象。例如：

.. code-block:: shell

   mc mv play/mybucket/object.txt play/mybucket/myobject.txt

对于递归移动操作（:mc-cmd:`mc mv --recursive`），MinIO
会将 ``TARGET`` 路径视为 ``SOURCE`` 上对象的前缀。

校验和验证
~~~~~~~~~~~~~~~~~~~~~

:mc:`mc mv` 使用 MD5SUM 校验和验证所有到对象存储的移动操作。

MinIO 在对象删除时会裁剪空前缀
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. |command| replace:: :mc:`mc mv`

.. include:: /includes/common-admonitions.rst
   :start-after: start-remove-api-trims-prefixes
   :end-before: end-remove-api-trims-prefixes

.. include:: /includes/common-admonitions.rst
   :start-after: start-remove-api-trims-prefixes-fs
   :end-before: end-remove-api-trims-prefixes-fs

S3 兼容性
~~~~~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
