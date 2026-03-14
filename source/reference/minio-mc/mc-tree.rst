===========
``mc tree``
===========

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc tree

.. |command| replace:: :mc:`mc tree`
.. |rewind| replace:: :mc-cmd:`~mc tree --rewind`
.. |alias| replace:: :mc-cmd:`~mc tree ALIAS`

语法
------

.. start-mc-tree-desc

:mc:`mc tree` 命令以树形格式列出 MinIO 存储桶中的所有前缀。
该命令还可选支持在每个前缀处列出存储桶内的所有对象，包括存储桶根。

.. end-mc-tree-desc

你也可以将 :mc:`mc tree` 用于本地文件系统目录，
获得与 ``tree`` 命令行工具类似的结果。

.. tab-set::

   .. tab-item:: 示例

      以下命令会打印 ``myminio`` MinIO 部署中 ``mydata`` 存储桶内
      任意深度的全部对象树：

      .. code-block:: shell
         :class: copyable

         mc tree --files myminio/mydata

   .. tab-item:: 语法

      该命令具有以下语法：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] tree                 \
                          [--depth int]        \
                          [--files]            \
                          [--rewind "string"]  \

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~~~~~~~

.. mc-cmd:: ALIAS

   *必需* MinIO 部署的 :ref:`alias <alias>` 和存储桶的完整路径，
   用于列出树形层级。例如：

   .. code-block:: shell

      mc tree myminio/mybucket

   你可以为 :mc:`mc tree` 命令指定多个目标。例如：

   .. code-block:: shell

      mc tree myminio/mybucket myminio/myotherbucket

   如需获取本地文件系统目录的树形层级，
   请指定该目录的完整路径。例如：

   .. code-block:: shell

      mc tree ~/minio/mydata/

.. mc-cmd:: --depth, d
   

   *可选* 将树深度限制为指定的整数值。
   
   默认为 ``-1``，即不限制深度。

.. mc-cmd:: --files, f
   

   *可选* 在 :mc:`mc tree` 输出中包含对象或目录中的文件。

.. mc-cmd:: --rewind
   :optional:

   .. include:: /includes/facts-versioning.rst
      :start-after: start-rewind-desc
      :end-before: end-rewind-desc

示例
--------

.. code-block:: shell
   :class: copyable

   mc tree ALIAS/PATH

- 将 :mc-cmd:`ALIAS <mc tree ALIAS>` 替换为 MinIO 部署的 :ref:`alias <alias>`。

- 将 :mc-cmd:`PATH <mc tree ALIAS>` 替换为 MinIO 部署上存储桶的路径。


行为
--------

S3 兼容性
~~~~~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
