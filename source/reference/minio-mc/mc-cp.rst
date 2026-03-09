.. _minio-mc-cp:

=========
``mc cp``
=========

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc cp

.. |command| replace:: :mc:`mc cp`
.. |rewind| replace:: :mc-cmd:`~mc cp --rewind`
.. |versionid| replace:: :mc-cmd:`~mc cp --version-id`
.. |alias| replace:: :mc-cmd:`~mc cp SOURCE`

语法
----

.. start-mc-cp-desc

:mc:`mc cp` 命令用于在 MinIO 部署与本地文件系统之间复制对象，
其中源端可以是 MinIO *或* 本地文件系统。

.. end-mc-cp-desc

你也可以将 :mc:`mc cp` 用于本地文件系统，达到与 ``cp`` 命令行工具
类似的效果。

.. note::

   :mc:`mc cp` 仅复制对象的最新版本或指定版本，不包含任何版本信息或修改日期。
   要复制所有版本、版本信息及相关元数据，请使用 :mc:`mc replicate add` 或 :mc:`mc admin replicate`。

.. tab-set::

   .. tab-item:: 示例

      以下命令将文件从本地文件系统目录复制到 ``myminio`` MinIO 部署中的
      ``mydata`` 存储桶：

      .. code-block:: shell
         :class: copyable

         mc cp --recursive ~/mydata/ myminio/mydata/

   .. tab-item:: 语法

      :mc:`mc cp` 命令的语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] cp                                                        \
                          [--attr "string"]                                         \
                          [--disable-multipart]                                     \
                          [--enc-kms "string"]                                      \
                          [--enc-s3 "string"]                                       \
                          [--enc-c "string"]                                        \
                          [--legal-hold "on"]                                       \
                          [--limit-download string]                                 \
                          [--limit-upload string]                                   \
                          [--md5]                                                   \
                          [--newer-than "string"]                                   \
                          [--older-than "string"]                                   \
                          [--preserve]                                              \
                          [--recursive]                                             \
                          [--retention-mode "string" --retention-duration "string"] \
                          [--rewind "string"]                                       \
                          [--storage-class "string"]                                \
                          [--tags "string"]                                         \
                          [--version-id "string"]                                   \
                          [--zip]                                                   \
                          SOURCE [SOURCE ...]                                       \
                          TARGET

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~~~

.. mc-cmd:: SOURCE
   :required:

   要复制的对象。

   如需从 MinIO 复制对象，请指定该对象的 :ref:`alias <alias>` 和完整路径
   （例如存储桶和对象路径）。
   例如：

   .. code-block:: none

      mc cp play/mybucket/object.txt ~/mydata/object.txt

   指定多个 ``SOURCE`` 路径可将多个对象复制到指定的 :mc-cmd:`~mc cp TARGET`。
   :mc:`mc cp` 将最后一个指定的 alias 或文件系统路径视为 ``TARGET``。
   例如：

   .. code-block:: none

      mc cp ~/data/object.txt myminio/mydata/object.txt play/mydata/

   如需从本地文件系统复制对象，请指定该对象的完整路径。
   例如：

   .. code-block:: none

      mc cp ~/mydata/object.txt play/mybucket/object.txt

   如果在 :mc-cmd:`~mc cp SOURCE` 中指定的是目录或存储桶，
   还必须指定 :mc-cmd:`~mc cp --recursive` 以递归复制该目录或存储桶中的内容。
   如果省略 ``--recursive`` 参数，:mc:`~mc cp` 仅复制指定目录或存储桶顶层中的对象。

.. mc-cmd:: TARGET
   :required:

   :mc:`mc cp` 复制对象的目标完整路径。

   如需将对象复制到 MinIO，
   请指定该对象的 :mc:`alias <mc alias>` 和完整路径
   （例如存储桶和对象路径）。例如：

   .. code-block:: none

      mc cp ~/mydata/object.txt play/mybucket/object.txt


   如需将对象复制到本地文件系统，请指定该对象的完整
   路径。例如：

   .. code-block:: none

      mc cp play/mybucket/object.txt ~/mydata/object.txt

.. mc-cmd:: --attr
   :optional:

   为对象添加自定义元数据。
   以 ``KEY=VALUE\;`` 格式指定键值对。
   例如，``--attr key1=value1\;key2=value2\;key3=value3``。

.. mc-cmd:: --checksum
   :optional:

   .. versionadded:: RELEASE.2024-10-02T08-27-28Z

   为上传对象添加校验和。

   有效值包括：
   - ``MD5``
   - ``CRC32``
   - ``CRC32C``
   - ``SHA1``
   - ``SHA256``

   该标志依赖服务端 trailing headers，适用于 AWS 或 MinIO 目标。

.. mc-cmd:: --disable-multipart
   :optional:

   为本次复制会话禁用分片上传。

.. block include of enc-c , enc-s3, and enc-kms

.. include:: /includes/common-minio-sse.rst
   :start-after: start-minio-mc-sse-options
   :end-before: end-minio-mc-sse-options

.. mc-cmd:: --legal-hold
   :optional:

   为复制后的对象启用无限期的 :ref:`legal hold <minio-object-locking-legalhold>` 对象锁定。

   指定 ``on``。

.. include:: /includes/linux/minio-client.rst
   :start-after: start-mc-limit-flags-desc
   :end-before: end-mc-limit-flags-desc

.. mc-cmd:: --md5
   :optional:

   .. versionchanged:: RELEASE.2024-10-02T08-27-28Z

      已由 :mc-cmd:`~mc cp --checksum` 标志替代。

   强制所有上传计算 MD5 校验和。

.. mc-cmd:: --newer-than
   :optional:

   复制比指定天数更新的对象。
   以 ``#d#hh#mm#ss`` 格式指定字符串。
   例如：``--older-than 1d2hh3mm4ss``

   默认为 ``0``（所有对象）。

.. mc-cmd:: --older-than
   :optional:

   复制早于指定时间限制的对象。
   以 ``#d#hh#mm#ss`` 格式指定字符串。
   例如：``--older-than 1d2hh3mm4ss``

   默认为 ``0``（所有对象）。

.. mc-cmd:: --preserve, a
   :optional:

   在 :mc-cmd:`~mc cp TARGET` 存储桶中保留来自 :mc-cmd:`~mc cp SOURCE` 目录、存储桶和对象的文件系统属性及存储桶策略规则。


.. mc-cmd:: --recursive, r
   :optional:

   将每个 :mc-cmd:`~mc cp SOURCE` 存储桶或目录中的内容递归复制到 :mc-cmd:`~mc cp TARGET` 存储桶。

.. mc-cmd:: --retention-duration
   :optional:

   要应用于复制对象的 :ref:`WORM retention mode <minio-object-locking-retention-modes>` 持续时间。

   以 ``#d#hh#mm#ss`` 格式的字符串指定持续时间。
   例如：``--retention-duration "1d2hh3mm4ss"``。

   需要同时指定 :mc-cmd:`~mc cp --retention-mode`。

.. mc-cmd:: --retention-mode
   :optional:

   在复制对象上启用 :ref:`object locking mode <minio-object-locking-retention-modes>`。
   支持以下取值：

   - ``GOVERNANCE``
   - ``COMPLIANCE``

   需要同时指定 :mc-cmd:`~mc cp --retention-duration`。

.. mc-cmd:: --rewind
   :optional:

   .. include:: /includes/facts-versioning.rst
      :start-after: start-rewind-desc
      :end-before: end-rewind-desc

.. mc-cmd:: --storage-class, sc
   :optional:

   为 :mc-cmd:`~mc cp TARGET` 上的新对象设置存储类。

   有关 S3 存储类的更多信息，请参见
   :aws-docs:`AmazonS3/latest/dev/storage-class-intro.html`。

.. mc-cmd:: --tags
   :optional:

   为复制后的对象应用一个或多个标签。

   以 ``KEY1=VALUE1&KEY2=VALUE2`` 形式指定由与号分隔的键值对，
   其中每一对代表分配给对象的一个标签。

.. mc-cmd:: --version-id, vid
   :optional:

   .. include:: /includes/facts-versioning.rst
      :start-after: start-version-id-desc
      :end-before: end-version-id-desc

.. mc-cmd:: --zip
   :optional:

   在复制期间，从 `.zip` 归档中提取文件。
   仅当源归档文件位于 MinIO 部署中时生效。

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

将对象复制到 S3
~~~~~~~~~~~~~~~~

使用 :mc:`mc cp` 将对象复制到 S3 兼容主机：

.. tab-set::

   .. tab-item:: 文件系统到 S3

      .. code-block:: shell
         :class: copyable

         mc cp SOURCE ALIAS/PATH

      - 将 :mc-cmd:`SOURCE <mc cp SOURCE>` 替换为对象的文件系统路径。

      - 将 :mc-cmd:`ALIAS <mc cp TARGET>` 替换为已配置的 S3 兼容主机的
        :mc:`alias <mc alias>`。

      - 将 :mc-cmd:`PATH <mc cp TARGET>` 替换为 S3 兼容主机上的对象路径。
        你可以指定不同的对象名称，以便在复制时“重命名”对象。

   .. tab-item:: S3 到 S3

      .. code-block:: shell
         :class: copyable

         mc cp SRCALIAS/SRCPATH TGTALIAS/TGTPATH

      - 将 :mc-cmd:`SRCALIAS <mc cp SOURCE>` 替换为源 S3 兼容主机的
        :mc:`alias <mc alias>`。

      - 将 :mc-cmd:`SRCPATH <mc cp SOURCE>` 替换为 S3 兼容主机上对象的路径。

      - 将 :mc-cmd:`TGTALIAS <mc cp TARGET>` 替换为目标 S3 兼容主机的
        :mc:`alias <mc alias>`。

      - 将 :mc-cmd:`TGTPATH <mc cp TARGET>` 替换为目标 S3 兼容主机上对象的路径。
        省略对象名称可使用 ``SRCPATH`` 的对象名称。

递归复制对象到 S3
~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc cp --recursive` 将对象递归复制到 S3 兼容主机：

.. tab-set::

   .. tab-item:: 文件系统到 S3

      .. code-block:: shell
         :class: copyable

         mc cp --recursive SOURCE ALIAS/PATH

      - 将 :mc-cmd:`SOURCE <mc cp SOURCE>` 替换为包含文件的目录路径。

      - 将 :mc-cmd:`ALIAS <mc cp TARGET>` 替换为已配置的 S3 兼容主机的
        :mc:`alias <mc alias>`。

      - 将 :mc-cmd:`PATH <mc cp TARGET>` 替换为 S3 兼容主机上的对象路径。
        :mc:`mc cp` 在目标主机创建对象时会使用 ``SOURCE`` 的文件名。

   .. tab-item:: S3 到 S3

      .. code-block:: shell
         :class: copyable

         mc cp --recursive SRCALIAS/SRCPATH TGTALIAS/TGTPATH

      - 将 :mc-cmd:`SRCALIAS <mc cp SOURCE>` 替换为源 S3 兼容主机的
        :mc:`alias <mc alias>`。

      - 将 :mc-cmd:`SRCPATH <mc cp SOURCE>` 替换为源 S3 兼容主机上的
        存储桶或存储桶前缀路径。

      - 将 :mc-cmd:`TGTALIAS <mc cp TARGET>` 替换为目标 S3 兼容主机的
        :mc:`alias <mc alias>`。

      - 将 :mc-cmd:`TGTPATH <mc cp TARGET>` 替换为目标 S3 兼容主机上的对象路径。
        :mc:`mc cp` 在目标主机创建对象时会使用 ``SRCPATH`` 的对象名称。

复制对象的时间点版本
~~~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc cp --rewind` 复制对象在某个特定时间点的状态。
该命令仅适用于 S3 到 S3 复制。

.. code-block:: shell
   :class: copyable

   mc cp --rewind DURATION SRCALIAS/SRCPATH TGTALIAS/TGTPATH

- 将 :mc-cmd:`DURATION <mc cp --rewind>` 替换为要回溯复制对象的过去时间点。
  例如，指定 ``30d`` 可复制当前日期前 30 天的对象版本。

- 将 :mc-cmd:`SRCALIAS <mc cp SOURCE>` 替换为源 S3 兼容主机的
  :mc:`alias <mc alias>`。

- 将 :mc-cmd:`SRCPATH <mc cp SOURCE>` 替换为源 S3 兼容主机上对象的路径。

- 将 :mc-cmd:`TGTALIAS <mc cp TARGET>` 替换为目标 S3 兼容主机的
  :mc:`alias <mc alias>`。

- 将 :mc-cmd:`TGTPATH <mc cp TARGET>` 替换为目标 S3 兼容主机上对象的路径。
  省略对象名称可使用 ``SRCPATH`` 的对象名称。

.. include:: /includes/facts-versioning.rst
   :start-after: start-versioning-admonition
   :end-before: end-versioning-admonition

复制对象的特定版本
~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc cp --version-id` 复制对象的特定版本。
该命令仅适用于 S3 到 S3 复制。

.. code-block:: shell
   :class: copyable

   mc cp --version-id VERSION SRCALIAS/SRCPATH TGTALIAS/TGTPATH

- 将 :mc-cmd:`VERSION <mc cp --rewind>` 替换为要复制的对象版本。

- 将 :mc-cmd:`SRCALIAS <mc cp SOURCE>` 替换为源 S3 兼容主机的
  :mc:`alias <mc alias>`。

- 将 :mc-cmd:`SRCPATH <mc cp SOURCE>` 替换为源 S3 兼容主机上对象的路径。

- 将 :mc-cmd:`TGTALIAS <mc cp TARGET>` 替换为目标 S3 兼容主机的
  :mc:`alias <mc alias>`。

- 将 :mc-cmd:`TGTPATH <mc cp TARGET>` 替换为目标 S3 兼容主机上对象的路径。
  省略对象名称可使用 ``SRCPATH`` 的对象名称。

.. include:: /includes/facts-versioning.rst
   :start-after: start-versioning-admonition
   :end-before: end-versioning-admonition

添加 ``content-type`` 值
~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc cp --attr` 添加 ``content-type`` 值。
该命令仅适用于 S3 到 S3 复制。

.. code-block:: shell
   :class: copyable

   mc cp --attr="content-type=CONTENT-TYPE" SRCALIAS/SRCPATH TGTALIAS/TGTPATH

- 将 ``CONTENT-TYPE`` 替换为所需的 content type（也称为 `media type <https://www.iana.org/assignments/media-types/media-types.xhtml>`__）。

- 将 :mc-cmd:`SRCALIAS <mc cp SOURCE>` 替换为源 S3 兼容主机的 :mc:`alias <mc alias>`。

- 将 :mc-cmd:`SRCPATH <mc cp SOURCE>` 替换为源 S3 兼容主机上对象的路径。

- 将 :mc-cmd:`TGTALIAS <mc cp TARGET>` 替换为目标 S3 兼容主机的 :mc:`alias <mc alias>`。

- 将 :mc-cmd:`TGTPATH <mc cp TARGET>` 替换为目标 S3 兼容主机上对象的路径。
  省略对象名称可使用 ``SRCPATH`` 的对象名称。

以下示例将 ``content-type`` 设置为 ``application/json``：

.. code-block::
   :class: copyable

    mc cp data.ndjson --attr="content-type=application/json" myminio/mybucket


行为
----

:mc:`mc cp` 使用 MD5SUM 校验和验证所有到对象存储的复制操作。

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
