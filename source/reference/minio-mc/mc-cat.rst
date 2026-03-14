.. _minio-mc-cat:

==========
``mc cat``
==========

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc cat

.. Replacement substitutions

.. |command| replace:: :mc:`mc cat`
.. |rewind| replace:: :mc-cmd:`~mc cat --rewind`
.. |versionid| replace:: :mc-cmd:`~mc cat --version-id`
.. |alias| replace:: :mc-cmd:`~mc cat ALIAS`

语法
----

.. start-mc-cat-desc

:mc:`mc cat` 命令将文件或对象的内容连接到另一个文件或对象。
你也可以使用该命令将指定文件或对象的内容输出到 ``STDOUT``。
:mc:`~mc cat` 的功能与 ``cat`` 类似。

.. end-mc-cat-desc

.. tab-set::

   .. tab-item:: 示例

      以下命令将 MinIO 部署中某个对象的内容输出到 ``STDOUT``：

      .. code-block:: shell
         :class: copyable

         mc cat play/mybucket/myobject.txt

   .. tab-item:: 语法

      :mc:`mc cat` 命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] cat                       \
                          ALIAS [ALIAS ...]         \
                          [--enc-c "value"]         \
                          [--offset "int"]          \
                          [--part-number "int"]     \
                          [--rewind]                \
                          [--tail "int"]            \
                          [--version-id "string"]   \
                          [--zip] 

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


你也可以将 :mc:`mc cat` 用于本地文件系统，以获得与 ``cat`` 命令行工具类似的结果。

参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   MinIO 部署的 :ref:`alias <alias>` 以及对象的完整路径。例如：

   .. code-block:: shell

      mc cat myminio/mybucket/myobject.txt

   你可以指定同一或不同 MinIO 部署中的多个对象。例如：

   .. code-block:: shell

      mc cat myminio/mybucket/object.txt myminio/myotherbucket/object.txt

   对于本地文件系统上的对象，请指定该对象的完整路径。例如：

   .. code-block:: shell

      mc cat ~/data/object.txt

.. block include of enc-c

.. include:: /includes/common-minio-sse.rst
   :start-after: start-minio-mc-sse-c-only
   :end-before: end-minio-mc-sse-options

.. mc-cmd:: --offset
   :optional:

   指定一个整数，表示命令输出的字节偏移位置。

   与 :mc-cmd:`~mc cat --part-number` 标志互斥。

.. mc-cmd:: --part-number
   :optional:

   下载分段上传中的指定分段编号。
   指定要下载的分段编号整数值。

   与 :mc-cmd:`~mc cat --offset` 和 :mc-cmd:`~mc cat --tail` 标志互斥。

.. mc-cmd:: --rewind
   :optional:

   .. include:: /includes/facts-versioning.rst
      :start-after: start-rewind-desc
      :end-before: end-rewind-desc

.. mc-cmd:: --tail
   :optional:

   指定一个整数，表示命令从该字节数开始裁剪输出。

   与 :mc-cmd:`~mc cat --part-number` 标志互斥。

.. mc-cmd:: --version-id, vid
   :optional:

   .. include:: /includes/facts-versioning.rst
      :start-after: start-version-id-desc
      :end-before: end-version-id-desc

.. mc-cmd:: --zip
   :optional:

   将源端 zip 文件中的内容提取到远端。
   要求源 ``ALIAS`` 为 MinIO 部署。

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

查看 S3 对象
~~~~~~~~~~~~

使用 :mc:`mc cat` 返回对象：

.. code-block:: shell
   :class: copyable

   mc cat ALIAS/PATH

- 将 :mc-cmd:`ALIAS <mc cat ALIAS>` 替换为 S3 兼容主机的
  :mc:`alias <mc alias>`。

- 将 :mc-cmd:`PATH <mc cat ALIAS>` 替换为对象在
  S3 兼容主机上的路径。

按时间点查看 S3 对象
~~~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc cat --rewind` 返回过去某个特定时间点的对象：

.. code-block:: shell
   :class: copyable

   mc cat ALIAS/PATH --rewind DURATION

- 将 :mc-cmd:`ALIAS <mc cat ALIAS>` 替换为 S3 兼容主机的
  :mc:`alias <mc alias>`。

- 将 :mc-cmd:`PATH <mc cat ALIAS>` 替换为对象在
  S3 兼容主机上的路径。

- 将 :mc-cmd:`DURATION <mc cat --rewind>` 替换为命令返回对象时对应的
  过去时间点。例如，指定 ``30d`` 可返回当前日期前 30 天的对象版本。

.. include:: /includes/facts-versioning.rst
   :start-after: start-versioning-admonition
   :end-before: end-versioning-admonition

查看 S3 对象的指定版本
~~~~~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc cat --version-id` 返回对象的特定版本：

.. code-block:: shell

   mc cat ALIAS/PATH --version-id VERSION

- 将 :mc-cmd:`ALIAS <mc cat ALIAS>` 替换为 S3 兼容主机的
  :mc:`alias <mc alias>`。

- 将 :mc-cmd:`PATH <mc cat ALIAS>` 替换为对象在
  S3 兼容主机上的路径。

- 将 :mc-cmd:`VERSION <mc cat --version-id>` 替换为要返回的对象特定版本。

.. include:: /includes/facts-versioning.rst
   :start-after: start-versioning-admonition
   :end-before: end-versioning-admonition

下载特定分段
~~~~~~~~~~~~

使用 :mc-cmd:`mc cat --part-number` 下载分段上传中的特定分段：

.. code-block:: shell
   :class: copyable

   mc cat ALIAS/PATH --part-number=#

- 将 :mc-cmd:`ALIAS <mc cat ALIAS>` 替换为 S3 兼容主机的 :mc:`alias <mc alias>`。

- 将 :mc-cmd:`PATH <mc cat ALIAS>` 替换为对象在 S3 兼容主机上的路径。

- 将 ``#`` 替换为要下载的分段编号整数值。
  例如，要下载一个 16 段分段文件中的第 3 段，使用 ``--part-number=3``。

如果使用了 ``--offset`` 或 ``--tail`` 标志，则不能使用 ``--part-number`` 标志。

行为
----

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
