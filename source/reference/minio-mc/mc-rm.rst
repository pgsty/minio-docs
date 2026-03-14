=========
``mc rm``
=========

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc rm

.. |command| replace:: :mc:`mc rm`
.. |rewind| replace:: :mc-cmd:`~mc rm --rewind`
.. |versions| replace:: :mc-cmd:`~mc rm --versions`
.. |versionid| replace:: :mc-cmd:`~mc rm --version-id`
.. |alias| replace:: :mc-cmd:`~mc rm ALIAS`

语法
------

.. start-mc-rm-desc

:mc:`mc rm` 命令用于从 MinIO 部署的存储桶中 :ref:`删除对象 <minio-object-delete>`。
如需彻底删除存储桶，请改用 :mc:`mc rb`。

.. end-mc-rm-desc

你也可以对本地文件系统使用 :mc:`mc rm`，以获得与 ``rm`` 命令行工具类似的结果。

有关 MinIO 如何对对象执行 ``DELETE`` 操作的更多信息，请参见 :ref:`minio-object-delete`。

.. important::

   :mc:`mc rm` 支持在一条命令中删除多个对象 *或* 文件。
   建议使用 :mc-cmd:`~mc rm --dry-run`
   选项验证操作仅作用于预期的对象/文件。

.. tab-set::

   .. tab-item:: 示例

      以下命令从 ``myminio`` MinIO 部署的 ``mydata``
      存储桶中删除多个对象：

      .. code-block:: shell
         :class: copyable

         mc rm --recursive myminio/mydata

   .. tab-item:: 语法

      命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] rm  \
                          [--bypass]               \
                          [--dangerous]            \
                          [--dry-run]              \
                          [--force]*               \
                          [--incomplete]           \
                          [--newer-than "string"]  \
                          [--non-current]          \
                          [--older-than "string"]  \
                          [--recursive]            \
                          [--rewind "string"]      \
                          [--stdin]                \
                          [--version-id "string"]* \
                          [--versions]             \
                          ALIAS [ALIAS ...]

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

      :mc-cmd:`mc rm --force` 是多个参数的必需项。
      :mc-cmd:`mc rm --version-id` 与多个
      参数互斥。更多信息请参见参考文档。

参数
~~~~~~~~~~

.. mc-cmd:: ALIAS
   :required:

   MinIO 部署的 :ref:`alias <alias>`，以及要删除对象的完整路径。
   例如：

   .. code-block:: shell

      mc rm play/mybucket/object.txt

   你可以在同一个或不同的 MinIO 部署上指定多个对象。
   例如：

   .. code-block:: shell

      mc rm play/mybucket/object.txt play/mybucket/otherobject.txt

   如果指定的是存储桶或存储桶前缀路径，**必须** 同时
   指定 :mc-cmd:`~mc rm --recursive` 和
   :mc-cmd:`~mc rm --force` 参数。例如：

   .. code-block:: shell

      mc rm --recursive --force play/mybucket/

      mc rm --recursive --force play/mybucket/myprefix/

   建议先使用
   :mc-cmd:`~mc rm --dry-run` 标志运行命令，验证
   递归删除操作的范围。

   若要从本地文件系统删除文件，请指定该文件的完整路径：

   .. code-block:: shell

      mc rm ~/data/myoldobject.txt

.. mc-cmd:: --bypass
   :optional:

   允许删除受 :ref:`GOVERNANCE <minio-object-locking-governance>`
   对象锁保护的对象。

.. mc-cmd:: --dangerous
   :optional:

   当 :mc-cmd:`~mc rm ALIAS` 指定 MinIO 部署的根路径（所有存储桶）时，
   允许执行 :mc:`mc rm`。

   与 :mc-cmd:`~mc rm --versions` 结合使用时，
   此标志会指示 :mc:`mc rm` 永久删除 ``ALIAS`` 目标中的所有对象
   *以及* 所有版本。

   建议先使用 :mc-cmd:`~mc rm --dry-run` 运行命令，
   验证站点范围删除操作的范围。

   .. warning::

      搭配 :mc-cmd:`~mc rm --versions` 标志运行
      :mc-cmd:`mc rm --dangerous` 是不可逆操作。执行前请尽可能
      审慎核查，确保命令只应用于预期的 ``ALIAS`` 目标。

.. mc-cmd:: --dry-run
   :optional:

   输出命令结果，但不会实际删除任何文件。
   使用此标志可测试命令配置是否仅删除你希望删除的对象。

.. mc-cmd:: --force
   :optional:

   允许 :mc:`mc rm` 与以下任一参数一起运行：

   - :mc-cmd:`~mc rm --recursive`
   - :mc-cmd:`~mc rm --versions`
   - :mc-cmd:`~mc rm --stdin`

.. mc-cmd:: --incomplete, I
   :optional:

   删除指定对象的不完整上传。

   如果任一 :mc-cmd:`~mc rm ALIAS` 指定了存储桶，
   则 **必须** 同时指定 :mc-cmd:`~mc rm --recursive`
   和 :mc-cmd:`~mc rm --force`。

.. mc-cmd:: --newer-than
   :optional:

   删除新于指定时长的对象。请使用 ``#d#hh#mm#ss``
   格式的字符串。例如：``--newer-than 1d2hh3mm4ss``

   默认为 ``0``（所有对象）。

.. mc-cmd:: --non-current
   :optional:

   从指定的 :mc-cmd:`~mc rm ALIAS` 中删除所有
   :ref:`非当前 <minio-bucket-versioning-delete>` 对象版本。

   对未启用 :ref:`版本控制 <minio-bucket-versioning>`
   的存储桶，此选项无效。

.. mc-cmd:: --older-than
   :optional:

   删除早于指定时间限制的对象。请使用
   ``#d#h#m#s`` 格式的字符串。例如：``--older-than 1d2h3m4s``。

   默认为 ``0``（所有对象）。

.. mc-cmd:: --recursive, r
   :optional:

   递归删除每个 :mc-cmd:`~mc rm ALIAS`
   存储桶或存储桶前缀的内容。

   如果指定 :mc-cmd:`~mc rm --recursive`，则 **必须** 同时
   指定 :mc-cmd:`~mc rm --force`。

   对启用了 :ref:`版本控制 <minio-bucket-versioning>` 的存储桶，
   此选项默认会为每个被删除对象生成一个删除标记。
   添加 :mc-cmd:`~mc rm --versions` 标志可递归删除存储桶中的
   所有对象 *以及* 对象版本。

   建议先使用
   :mc-cmd:`~mc rm --dry-run` 标志运行命令，验证
   递归删除操作的范围。

   与 :mc-cmd:`mc rm --version-id` 互斥

.. mc-cmd:: --rewind
   :optional:

   .. include:: /includes/facts-versioning.rst
      :start-after: start-rewind-desc
      :end-before: end-rewind-desc

.. mc-cmd:: --stdin
   :optional:

   从 ``STDIN`` 读取对象名称或存储桶。

.. mc-cmd:: --versions
   :optional:

   .. include:: /includes/facts-versioning.rst
      :start-after: start-versions-desc
      :end-before: end-versions-desc

   将 :mc-cmd:`~mc rm --versions` 与
   :mc-cmd:`~mc rm --rewind` 一起使用，可删除某一特定时间点
   存在的对象的所有版本。

.. mc-cmd:: --version-id, vid
   :optional:

   .. include:: /includes/facts-versioning.rst
      :start-after: start-version-id-desc
      :end-before: end-version-id-desc

   与以下任一标志互斥：

   - :mc-cmd:`~mc rm --versions`
   - :mc-cmd:`~mc rm --rewind`
   - :mc-cmd:`~mc rm --recursive`

全局标志
~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
--------

删除单个对象
~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: shell
   :class: copyable

   mc rm ALIAS/PATH

- 将 :mc-cmd:`ALIAS <mc rm ALIAS>` 替换为已配置的
  S3 兼容服务的 :mc:`alias <mc alias>`。

- 将 :mc-cmd:`PATH <mc rm ALIAS>` 替换为对象路径。


递归删除存储桶内容
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

将 :mc:`mc rm` 与
:mc-cmd:`~mc rm --recursive` 和 :mc-cmd:`~mc rm --force` 选项结合使用，
可递归删除存储桶内容。

.. code-block:: shell
   :class: copyable

   mc rm --recursive --force ALIAS/PATH

- 将 :mc-cmd:`ALIAS <mc rm ALIAS>` 替换为已配置的
  S3 兼容服务的 :mc:`alias <mc alias>`。

- 将 :mc-cmd:`PATH <mc rm ALIAS>` 替换为存储桶路径。

此操作 *不会* 删除存储桶本身。请使用 :mc:`mc rb` 删除存储桶
及其全部内容和关联配置。

删除对象的所有不完整上传文件
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

将 :mc:`mc rm` 与 :mc-cmd:`~mc rm --incomplete` 选项结合使用，
可删除某个对象的不完整上传文件。

.. code-block:: shell
   :class: copyable

   mc rm --incomplete --recursive --force ALIAS/PATH

- 将 :mc-cmd:`ALIAS <mc rm ALIAS>` 替换为已配置的
  S3 兼容服务的 :mc:`alias <mc alias>`。

- 将 :mc-cmd:`PATH <mc rm ALIAS>` 替换为对象路径。


将对象回滚到先前版本
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

将 :mc:`mc rm` 与 :mc-cmd:`~mc rm --versions` 和
:mc-cmd:`~mc rm --newer-than` 结合使用，可删除新于指定时长的
所有对象版本。这会有效地将对象“回滚”到该时间点的状态。

.. important::

   删除对象的特定版本属于 *破坏性* 操作。你无法恢复
   已删除的对象版本。

.. code-block:: shell
   :class: moveable

   mc rm ALIAS/PATH --versions --newer-than DURATION

- 将 :mc-cmd:`ALIAS <mc rm ALIAS>` 替换为已配置的
  S3 兼容服务的 :mc:`alias <mc alias>`。

- 将 :mc-cmd:`PATH <mc rm ALIAS>` 替换为对象路径。
  例如：``/mybucket/myobject``。

- 将 :mc-cmd:`DURATION <mc rm --newer-than>` 替换为相对于当前主机时间的过去时长，
  操作将从该时间点开始删除对象版本。例如，要删除过去 30 天内
  创建的所有对象版本，请指定 ``"30d"``。

行为
--------

删除存储桶内容
~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc:`mc rm` 删除存储桶中的全部内容时，不会删除存储桶本身。
与该存储桶关联的配置会保留，例如
:mc-cmd:`default object lock settings <mc retention set --default>`。

如需彻底删除存储桶，请使用 :mc:`mc rb`，而不是 :mc:`mc rm`。

MinIO 在删除对象时会清理空前缀
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. include:: /includes/common-admonitions.rst
   :start-after: start-remove-api-trims-prefixes
   :end-before: end-remove-api-trims-prefixes

.. include:: /includes/common-admonitions.rst
   :start-after: start-remove-api-trims-prefixes-fs
   :end-before: end-remove-api-trims-prefixes-fs

版本化存储桶中的删除操作
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

MinIO 支持在单个存储桶中保留对象的多个
:ref:`版本 <minio-bucket-versioning>`。
在版本化存储桶中 :ref:`删除 <minio-bucket-versioning-delete>` 对象时，
会生成一种特殊的 ``DeleteMarker`` 墓碑记录，将对象标记为已删除，
同时保留该对象的所有先前版本。

- 要从存储桶中删除对象的特定版本，请使用
  :mc-cmd:`mc rm --version-id`

- 要从存储桶中删除对象的所有版本，请使用
  :mc-cmd:`mc rm --versions`

- 要从存储桶中删除对象的所有非当前版本，请使用
  :mc-cmd:`mc rm --non-current`

.. versionchanged:: mc RELEASE.2023-03-20T17-17-53Z

   输出会显示版本化文件的修改时间。
   与 ``--dry-run`` 搭配使用时，这有助于确认你选择了正确的删除对象。

S3 兼容性
~~~~~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
