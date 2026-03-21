=============
``mc mirror``
=============

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc mirror

语法
------

.. start-mc-mirror-desc

:mc:`mc mirror` 命令用于将内容同步到 MinIO 部署，类似于 ``rsync`` 工具。
:mc:`mc mirror` 支持以文件系统、MinIO 部署和其他 S3 兼容主机作为同步源。

.. end-mc-mirror-desc

.. note::

   :mc:`mc mirror` 仅同步当前对象，不包含任何版本信息或元数据。
   若要同步对象的版本历史和元数据，可考虑对 :ref:`bucket replication <minio-bucket-replication-serverside>` 使用 :mc:`mc replicate`，或对 :ref:`site replication <minio-site-replication-overview>` 使用 :mc:`mc admin replicate`。


.. tab-set::

   .. tab-item:: EXAMPLE

      以下命令将本地文件系统目录中的内容同步到 ``myminio`` MinIO 部署上的 ``mydata`` 存储桶。

      .. code-block:: shell
         :class: copyable

         mc mirror --watch ~/mydata myminio/mydata

      该命令会“监视”本地文件系统中新增或删除的文件，并将这些操作同步到 MinIO，直到显式终止。 

      :mc-cmd:`mc mirror --watch` 会把本地文件系统中发生变更的文件更新到 MinIO（参见 :mc-cmd:`~mc mirror --overwrite`）。
      ``--watch`` 不会删除 MinIO 中本地文件系统不存在的其他文件（参见 :mc-cmd:`~mc mirror --remove`）。

   .. tab-item:: SYNTAX

      命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] mirror                            \
                          [--active-active]                 \
                          [--attr "string"]                 \
                          [--checksum "value"]              \
                          [--disable-multipart]             \
                          [--dry-run]                       \
                          [--enc-kms "string"]              \
                          [--enc-s3 "string"]               \
                          [--enc-c "string"]                \
                          [--exclude "string"]              \
                          [--exclude-bucket "string"]       \
                          [--exclude-storageclass "string"] \
                          [--limit-download string]         \
                          [--limit-upload string]           \
                          [--md5]                           \
                          [--monitoring-address "string"]   \
                          [--newer-than "string"]           \
                          [--older-than "string"]           \
                          [--overwrite]                     \
                          [--preserve]                      \
                          [--region "string"]               \
                          [--remove]                        \
                          [--retry]                         \
                          [--skip-errors]                   \
                          [--storage-class "string"]        \
                          [--summary]                       \
                          [--watch]                         \
                          SOURCE                            \
                          TARGET

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~~~~~~~

.. mc-cmd:: SOURCE
   :required:

   要同步到 :mc-cmd:`~mc mirror TARGET` S3 主机的文件或对象。

   对于 S3 兼容主机上的对象，请将对象路径指定为 ``ALIAS/PATH``，其中：

   - ``ALIAS`` 是已配置 S3 兼容主机的 :mc:`alias <mc alias>`，*并且*

   - ``PATH`` 是存储桶或对象路径。若指定存储桶，:mc:`mc mirror` 会同步该存储桶中的所有对象。

   .. code-block:: shell

      mc mirror [FLAGS] play/mybucket/ myminio/mybucket

   对于文件系统中的文件，请指定文件或目录的完整文件系统路径：

   .. code-block:: shell

      mc mirror [FLAGS] ~/data/ myminio/mybucket

   若指定的是目录，:mc:`mc mirror` 会同步该目录中的所有文件。

.. mc-cmd:: TARGET
   :required:

   :mc:`mc mirror` 用于同步 SOURCE 对象的目标存储桶完整路径。将 ``TARGET`` 指定为 ``ALIAS/PATH``，其中：

   - ``ALIAS`` 是已配置 S3 兼容主机的 :mc:`alias <mc alias>`，*并且*

   - ``PATH`` 是存储桶路径。

   .. code-block:: shell

      mc mirror SOURCE play/mybucket

   :mc:`mc mirror` 在同步到 ``TARGET`` 存储桶时，会使用 :mc-cmd:`~mc mirror SOURCE` 中对象或文件的名称。

.. mc-cmd:: --active-active
   :optional:

   在两个站点之间建立 active-active 镜像活动。
   必须在每个站点上重复执行该命令。

   例如：

   在站点 A 上，将 A 镜像到 B
   
   .. code-block::
      :class: copyable

      mc mirror --active-active siteA siteB

   在站点 B 上，将 B 镜像到 A

   .. code-block::
      :class: copyable

      mc mirror --active-active siteB siteA

.. mc-cmd:: --attr
   :optional:

   为镜像对象添加自定义元数据。将键值对指定为 ``KEY=VALUE\;``。 
   例如：``--attr key1=value1\;key2=value2\;key3=value3``。

.. mc-cmd:: --checksum
   :optional:

   .. versionadded:: RELEASE.2024-10-02T08-27-28Z

   为上传对象添加校验和。 
   
   有效值为： 
   - ``MD5``
   - ``CRC32``
   - ``CRC32C``
   - ``SHA1``
   - ``SHA256``

   该标志要求服务端支持 trailing headers，并适用于 AWS 或 MinIO 目标。

.. mc-cmd:: --disable-multipart
   :optional:

   为本次同步会话禁用 multipart 上传。

.. mc-cmd:: --dry-run
   :optional:

   执行一次模拟镜像操作。
   使用该操作可测试 :mc:`mc mirror` 是否只会镜像预期的对象或存储桶。

.. block include of enc-c , enc-s3, and enc-kms

.. include:: /includes/common-minio-sse.rst
   :start-after: start-minio-mc-sse-options
   :end-before: end-minio-mc-sse-options

.. mc-cmd:: --exclude
   :optional:

   排除 :mc-cmd:`~mc mirror SOURCE` 路径中与指定对象 :ref:`name pattern <minio-wildcard-matching>` 匹配的对象。

.. mc-cmd:: --exclude-bucket
   :optional:

   .. versionadded:: mc RELEASE.2024-03-03T00-13-08Z

   排除 :mc-cmd:`~mc mirror SOURCE` 路径中与指定存储桶 :ref:`name pattern <minio-wildcard-matching>` 匹配的存储桶。

.. mc-cmd:: --exclude-storageclass
   :optional:

   排除 :mc-cmd:`~mc mirror SOURCE` 上具有指定存储类的对象。
   可在同一命令中多次使用该标志，以排除多个存储类中的对象。

   可用于排除需要重新水化或恢复的存储类对象，例如从 AWS S3 存储桶迁移时，某些对象使用 ``GLACIER`` 或 ``DEEP_ARCHIVE`` 存储类。

.. --limit-download and --limit-upload included here

.. include:: /includes/linux/minio-client.rst
   :start-after: start-mc-limit-flags-desc
   :end-before: end-mc-limit-flags-desc

.. mc-cmd:: --md5
   :optional:

   强制所有上传计算 MD5 校验和。

.. mc-cmd:: --monitoring-address
   :optional:

   创建一个用于监控镜像活动的 `Prometheus <https://prometheus.io/>`__ endpoint。
   指定创建抓取 endpoint 的本地网络适配器和端口地址。
   默认为 ``localhost:8081``)。

.. mc-cmd:: --newer-than
   :optional:

   仅镜像比指定天数更新的对象。
   以 ``#d#hh#mm#ss`` 格式指定字符串
   例如：``--newer-than 1d2hh3mm4ss``。

.. mc-cmd:: --older-than
   :optional:

   仅镜像早于指定时间阈值的对象。
   以 ``#d#hh#mm#ss`` 格式指定字符串。
   例如：``--older-than 1d2hh3mm4ss``。

   默认为 ``0``（所有对象）。

.. mc-cmd:: --overwrite
   :optional:

   覆盖 :mc-cmd:`~mc mirror TARGET` 上的对象。

   例如，设想一个正在运行的 ``mc mirror --overwrite``，将内容从 Source 同步到 Destination。

   如果 Source 上的对象发生变更，``mc mirror --overwrite`` 会同步并覆盖 Destination 上的同名文件。

   如果不使用 ``--overwrite``，当对象已存在于 Destination 时，镜像进程将无法同步该对象。
   ``mc mirror`` 会记录错误并继续同步其他对象。

.. mc-cmd:: --preserve, a
   :optional:

   在 :mc-cmd:`~mc mirror TARGET` 上保留 :mc-cmd:`~mc mirror SOURCE` 的文件系统属性和存储桶策略规则。

.. mc-cmd:: --region
   :optional:

   在目标端创建新存储桶时，指定 ``string`` 区域。

   默认为 ``"us-east-1"``。

.. mc-cmd:: --remove
   :optional:

   删除 Target 上 Source 中不存在的对象。

   使用 ``--remove`` 标志可使 Source 和 Target 拥有相同的对象列表。

   例如，Source 上存在对象 A、B、C。
   Target 上存在对象 C、D、E。

   运行 ``mc mirror --remove`` 时，对象 A 和 B 会同步到 Target，对象 D 和 E 会从 Target 删除。
   由于对象 C 在两端都已存在，不会有任何内容从 Source 移动到 Target。 

   操作完成后，Source 和 Target 上都只存在对象 A、B、C。

   ``mc mirror --remove`` 不会校验对象 C 在 Source 和 Target 上的内容是否一致，只检查两端是否都存在名为 `C` 的对象。
   若要确保 Source 与 Target 上对象的名称和内容都一致，请使用 :mc-cmd:`~mc mirror --overwrite` 或 :mc-cmd:`~mc mirror --watch`。

   .. versionchanged:: RELEASE.2023-05-04T18-10-16Z

      如果目标路径是不存在的本地文件系统目录，``mc mirror --remove`` 会返回错误。

      在早期版本中，如果 ``directory`` 不存在，指定 ``/path/to/directory`` 会导致删除 ``/path/to`` 文件夹。

.. mc-cmd:: --retry
   :optional:

   镜像过程中出现错误时，对每个出错对象执行重试。

.. mc-cmd:: --storage-class, sc
   :optional:

   为 :mc-cmd:`~mc mirror TARGET` 上的新对象设置存储类。 

   有关 S3 存储类的更多信息，请参阅 Amazon 文档：:aws-docs:`Storage Classes <AmazonS3/latest/dev/storage-class-intro.html>`。

.. mc-cmd:: --skip-errors
   :optional:

   .. versionadded:: mc RELEASE.2024-01-28T16-23-14Z

   跳过镜像过程中产生错误的对象。

.. mc-cmd:: --summary
   :optional:

   完成后输出已同步数据的摘要。

.. mc-cmd:: --watch, w
   :optional:

   使用 ``--watch`` 标志可将对象从 Source 镜像到 Target，且 Target 也可以包含 Source 中不存在的其他对象。

   - ``--watch`` 会持续将文件从 Source 同步到 Target，直到显式终止
   - Target 可以包含 Source 中不存在的文件
   - 若 Source 中存在同名对象，``--watch`` 会覆盖 Target 上的对象，行为类似 :mc-cmd:`~mc mirror --overwrite`

   默认为 ``0``（所有对象）。

   例如，被监视的 Source 上存在对象 A 和 B。
   被监视的 Target 上存在对象 A、B、C。

   某客户端向 Source 写入对象 D，并删除对象 B。

   操作后，Source 上存在对象 A 和 D。
   Target 上存在对象 A、C、D。


全局标志
~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
--------

将本地目录镜像到 S3 兼容主机
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc:`mc mirror` 将文件从文件系统镜像到 S3 主机：

.. code-block::
   :class: copyable

   mc mirror FILEPATH ALIAS/PATH

- 将 :mc-cmd:`FILEPATH <mc mirror SOURCE>` 替换为要镜像目录的完整文件路径。

- 将 :mc-cmd:`ALIAS <mc mirror TARGET>` 替换为已配置 S3 兼容主机的 :mc-cmd:`alias <mc alias>`。

- 将 :mc-cmd:`PATH <mc mirror TARGET>` 替换为目标存储桶。

持续将本地目录镜像到 S3 兼容主机
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

将 :mc:`mc mirror` 与 :mc-cmd:`~mc mirror --watch` 一起使用，可持续将文件从文件系统镜像到 S3 兼容主机；文件系统中新增或删除的对象会在主机端同步新增或删除：

.. code-block::
   :class: copyable

   mc mirror --watch FILEPATH ALIAS/PATH

- 将 :mc-cmd:`FILEPATH <mc mirror SOURCE>` 替换为要镜像目录的完整文件路径。

- 将 :mc-cmd:`ALIAS <mc mirror TARGET>` 替换为已配置 S3 兼容主机的 :mc-cmd:`alias <mc alias>`。

- 将 :mc-cmd:`PATH <mc mirror TARGET>` 替换为目标存储桶。

持续将 S3 存储桶镜像到 S3 兼容主机
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

将 :mc:`mc mirror` 与 :mc-cmd:`~mc mirror --watch` 一起使用，可持续将一个 S3 兼容主机上某个存储桶中的对象镜像到另一个 S3 兼容主机；存储桶中新增或删除的对象会同步到目标主机。

.. code-block::
   :class: copyable

   mc mirror --watch SRCALIAS/SRCPATH TGTALIAS/TGTPATH

- 将 :mc-cmd:`SRCALIAS <mc mirror SOURCE>` 替换为已配置 S3 兼容主机的 :mc-cmd:`alias <mc alias>`。

- 将 :mc-cmd:`SRCPATH <mc mirror SOURCE>` 替换为要镜像的存储桶。

- 将 :mc-cmd:`TGTALIAS <mc mirror TARGET>` 替换为已配置 S3 兼容主机的 :mc-cmd:`alias <mc alias>`。

- 将 :mc-cmd:`TGTPATH <mc mirror TARGET>` 替换为目标存储桶。

将对象从 AWS S3 镜像到 MinIO，并跳过 GLACIER 中的对象
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

将 :mc:`mc mirror` 与 :mc-cmd:`~mc mirror --exclude-storageclass` 一起使用，可将对象从 AWS S3 镜像到 MinIO，同时不镜像 GLACIER 或 DEEP_ARCHIVE 存储中的对象。

.. code-block::
   :class: copyable

   mc mirror --exclude-storageclass GLACIER  \
      --exclude-storageclass DEEP_ARCHIVE SRCALIAS/SRCPATH TGALIAS/TGPATH

- 将 :mc-cmd:`SRCALIAS <mc mirror SOURCE>` 替换为已配置 S3 主机的 :mc-cmd:`alias <mc alias>`。

- 将 :mc-cmd:`SRCPATH <mc mirror SOURCE>` 替换为要镜像的存储桶。

- 将 :mc-cmd:`TGTALIAS <mc mirror TARGET>` 替换为已配置 S3 主机的 :mc-cmd:`alias <mc alias>`。

- 将 :mc-cmd:`TGTPATH <mc mirror TARGET>` 替换为目标存储桶。

行为
--------

对象失败时镜像继续执行
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

如果目标上存在同名对象，MinIO 会为重复对象输出错误。
发生错误后，``mc mirror`` 会继续将其他对象从源端镜像到目标端。

对象删除时 MinIO 会裁剪空前缀
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

:mc-cmd:`mc mirror --watch` 命令会持续同步源端和目标端中新增与删除的对象。
这也包括在源端删除对象时，自动删除目标端对应对象。

如需将源端更新的对象同步更新到目标端，请使用 `--overwrite`。
如需删除目标端中源端不存在的对象，请使用 `--remove`。

.. |command| replace:: :mc-cmd:`mc mirror --watch`

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
