.. _minio-mc-head:

===========
``mc head``
===========

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc::  mc head


.. |command| replace:: :mc:`mc head`
.. |rewind| replace:: :mc-cmd:`~mc head --rewind`
.. |versionid| replace:: :mc-cmd:`~mc head --version-id`
.. |alias| replace:: :mc-cmd:`~mc head ALIAS`

语法
----

.. start-mc-head-desc

:mc:`mc head` 命令显示对象的前 ``n`` 行，
其中 ``n`` 是传递给该命令的参数。

.. end-mc-head-desc

:mc:`mc head` 不会对对象内容执行任何转换或格式化来提升可读性。
你也可以将 :mc:`mc head` 用于本地文件系统，以获得与 ``head`` 命令行工具类似的结果。

.. tab-set::

   .. tab-item:: 示例

      以下命令返回 ``myminio`` MinIO 部署中 ``mydata`` 存储桶内某个对象的前 10 行：

      .. code-block:: shell
         :class: copyable

         mc head myminio/mydata/myobject.txt

   .. tab-item:: 语法

      该命令的语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] head                     \
                          [--lines int]            \
                          [--rewind "string"]      \
                          [--version-id "string"]  \
                          [--enc-c "string"]       \
                          ALIAS [ALIAS ...]

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   要输出的一个或多个对象。

   对于 MinIO 上的对象，请指定 :ref:`alias <alias>` 和该对象的完整路径
   （例如存储桶和对象路径）。例如：

   .. code-block:: none

      mc head play/mybucket/object.txt

   你可以在同一个或不同的 MinIO 部署上指定多个对象。例如：

   .. code-block:: none

      mc head ~/mydata/object.txt myminio/mydata/object.txt

   对于本地文件系统上的对象，请指定该对象的完整路径。
   例如：

   .. code-block:: none

      mc head ~/mydata/object.txt

.. mc-cmd:: --lines, n
   :optional:

   要输出的行数。

   默认为 ``10``。

.. block include of enc-c

.. include:: /includes/common-minio-sse.rst
   :start-after: start-minio-mc-sse-c-only
   :end-before: end-minio-mc-sse-options

.. mc-cmd:: --rewind
   :optional:

   .. include:: /includes/facts-versioning.rst
      :start-after: start-rewind-desc
      :end-before: end-rewind-desc

.. mc-cmd:: --version-id, vid
   :optional:

   .. include:: /includes/facts-versioning.rst
      :start-after: start-version-id-desc
      :end-before: end-version-id-desc


全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

查看对象的部分内容
~~~~~~~~~~~~~~~~~~

使用 :mc:`mc head` 返回对象的前 10 行：

.. code-block:: shell
   :class: copyable

   mc head ALIAS/PATH

- 将 :mc-cmd:`ALIAS <mc head ALIAS>` 替换为 S3 兼容主机的
  :mc:`alias <mc alias>`。

- 将 :mc-cmd:`PATH <mc head ALIAS>` 替换为该对象在 S3 兼容主机上的路径。

查看对象在某个时间点的部分内容
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc head --rewind` 返回对象在过去特定时间点的前 10 行：

.. code-block:: shell
   :class: copyable

   mc head ALIAS/PATH --rewind DURATION

- 将 :mc-cmd:`ALIAS <mc head ALIAS>` 替换为 S3 兼容主机的
  :mc:`alias <mc alias>`。

- 将 :mc-cmd:`PATH <mc head ALIAS>` 替换为该对象在 S3 兼容主机上的路径。

- 将 :mc-cmd:`DURATION <mc head --rewind>` 替换为过去的某个时间点，
  命令会返回该时间点对应的对象。例如，指定 ``30d`` 可返回当前日期前 30 天的对象版本。

.. include:: /includes/facts-versioning.rst
   :start-after: start-versioning-admonition
   :end-before: end-versioning-admonition

查看对象指定版本的部分内容
~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc head --version-id` 返回对象特定版本的前 10 行：

.. code-block:: shell
   :class: copyable

   mc head ALIAS/PATH --version-id VERSION

- 将 :mc-cmd:`ALIAS <mc head ALIAS>` 替换为 S3 兼容主机的
  :mc:`alias <mc alias>`。

- 将 :mc-cmd:`PATH <mc head ALIAS>` 替换为该对象在 S3 兼容主机上的路径。

- 将 :mc-cmd:`VERSION <mc head --version-id>` 替换为对象版本。
  例如，指定 ``30d`` 可返回当前日期前 30 天的对象版本。

.. include:: /includes/facts-versioning.rst
   :start-after: start-versioning-admonition
   :end-before: end-versioning-admonition


行为
----

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
