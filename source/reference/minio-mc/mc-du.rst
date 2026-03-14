.. _minio-mc-du:

==========
``mc du``
==========

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc du

.. Replacement substitutions

.. |command| replace:: :mc:`mc du`
.. |rewind| replace:: :mc-cmd:`~mc du --rewind`
.. |versions| replace:: :mc-cmd:`~mc du --versions`
.. |alias| replace:: :mc-cmd:`~mc du ALIAS`

语法
------

.. start-mc-du-desc

:mc:`mc du` 命令用于汇总存储桶和文件夹的磁盘使用量。
你也可以对本地文件系统使用 :mc:`~mc du`，以生成与 ``du`` 命令类似的结果。

.. end-mc-du-desc

.. tab-set::

   .. tab-item:: 示例

      以下命令打印 ``myminio`` MinIO 部署中 ``mybucket`` 存储桶的磁盘使用量：

      .. code-block:: shell
         :class: copyable

         mc du play/mybucket

      输出类似如下：
      
      .. code-block:: shell

         825KiB	3 objects        mybucket

   .. tab-item:: 语法

      :mc:`mc du` 命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] du                    \
                          [--depth]             \
                          [--recursive]         \
                          [--rewind]            \
                          [--versions]          \
                          ALIAS [ALIAS ...]

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


参数
~~~~~~~~~~

.. mc-cmd:: ALIAS
   :required:
   
   MinIO 部署的 :ref:`alias <alias>` 以及文件夹的完整路径。例如：

   .. code-block:: shell

      mc du myminio/mybucket

   你可以在同一个或不同的 MinIO 部署上指定多个存储桶和文件夹。例如：

   .. code-block:: shell

      mc du myminio/mybucket myminio/myotherbucket/myfolder

   对于本地文件系统中的文件夹，请指定该文件夹的完整路径。例如：

   .. code-block:: shell

      mc du ~/data/images

   :mc:`mc du` 完成所需时间取决于目标存储桶和文件夹的大小。大型存储桶可能需要一些时间来生成磁盘使用量摘要。
   
.. mc-cmd:: --depth, d
   :optional:

   打印命令中指定路径下 N 层及以内所有文件夹的总计值。默认值为 0，仅统计指定路径本身。

.. mc-cmd:: --recursive, r
   :optional:

   递归打印每个存储桶或子文件夹的总计值。

.. mc-cmd:: --rewind
   :optional:

   .. include:: /includes/facts-versioning.rst
      :start-after: start-rewind-desc
      :end-before: end-rewind-desc

   将 :mc-cmd:`~mc du --rewind` 与 :mc-cmd:`~mc du --versions` 一起使用，可显示特定时间点存在的对象版本的磁盘使用量。

.. mc-cmd:: --versions
   :optional:

   .. include:: /includes/facts-versioning.rst
      :start-after: start-versions-desc
      :end-before: end-versions-desc

   将 :mc-cmd:`~mc du --versions` 与 :mc-cmd:`~mc du --rewind` 一起使用，可显示特定时间点存在的对象版本的磁盘使用量。


全局标志
~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
--------

查看存储桶或文件夹的磁盘使用量
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc:`mc du` 打印存储桶或文件夹的磁盘使用量摘要：

.. code-block:: shell
   :class: copyable

   mc du ALIAS/PATH

- 将 ``ALIAS`` 替换为 S3 兼容主机的 :mc:`alias <mc alias>`。

- 将 ``PATH`` 替换为 S3 兼容主机上存储桶或文件夹的路径。

查看某个时间点的磁盘使用量
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc du --rewind` 打印过去某个特定时间点的磁盘使用量摘要：

.. code-block:: shell
   :class: copyable

   mc du --rewind DURATION ALIAS/PATH

- 将 ``DURATION`` 替换为所需的过去时间点。例如，指定 ``30d`` 以显示当前日期前 30 天的磁盘使用量。

- 将 ``ALIAS`` 替换为 S3 兼容主机的 :mc:`alias <mc alias>`。

- 将 ``PATH`` 替换为 S3 兼容主机上存储桶或文件夹的路径。

.. include:: /includes/facts-versioning.rst
   :start-after: start-versioning-admonition
   :end-before: end-versioning-admonition

递归查看磁盘使用量
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc du --recursive` 递归打印每个文件夹的摘要：

.. code-block:: shell
   :class: copyable

   mc du --recursive ALIAS/PATH

- 将 ``ALIAS`` 替换为 S3 兼容主机的 :mc:`alias <mc alias>`。

- 将 ``PATH`` 替换为 S3 兼容主机上存储桶或文件夹的路径。


行为
--------

S3 兼容性
~~~~~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
