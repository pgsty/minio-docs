.. _minio-mc-tag-remove:

=================
``mc tag remove``
=================

.. default-domain:: minio

.. contents:: Table of Contents
   :local:
   :depth: 2

.. mc:: mc tag remove

.. |command| replace:: :mc:`mc tag remove`
.. |rewind| replace:: :mc-cmd:`~mc tag remove --rewind`
.. |versions| replace:: :mc-cmd:`~mc tag remove --versions`
.. |versionid| replace:: :mc-cmd:`~mc tag remove --version-id`
.. |alias| replace:: :mc-cmd:`~mc tag remove ALIAS`

语法
----

.. start-mc-tag-remove-desc

:mc:`mc tag remove` 命令用于移除存储桶或对象上的所有标签。

.. end-mc-tag-remove-desc

.. tab-set::

   .. tab-item:: 示例
 
      以下命令会移除 ``myminio`` MinIO 部署中 ``mydata`` 存储桶的标签：

      .. code-block:: shell
         :class: copyable

         mc tag remove myminio/mydata

   .. tab-item:: 语法

      该命令的语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] tag remove                \
                          [--rewind "string"]       \
                          [--versions]              \
                          [--version-id "string"]*  \

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

      :mc-cmd:`mc tag remove --version-id` 与多个参数互斥。有关更多信息，请参阅参考文档。

参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   MinIO 部署的 :ref:`alias <alias>`，以及要移除全部标签的对象完整路径
   （例如存储桶和对象路径）。例如：

   .. code-block:: none

      mc tag remove myminio/mybucket/object.txt

.. mc-cmd:: --recursive, r
   :optional:

   .. versionadded:: RELEASE.2023-05-04T18-10-16Z

   递归移除指定 :mc:`ALIAS <mc tag remove ALIAS>` 下所有对象的全部标签。

.. mc-cmd:: --rewind
   :optional:

   .. include:: /includes/facts-versioning.rst
      :start-after: start-rewind-desc
      :end-before: end-rewind-desc

.. mc-cmd:: --versions
   :optional:   

   .. include:: /includes/facts-versioning.rst
      :start-after: start-versions-desc
      :end-before: end-versions-desc

   结合使用 :mc-cmd:`~mc tag remove --versions` 和
   :mc-cmd:`~mc tag remove --rewind`，可移除某个特定时间点存在的所有对象版本上的标签。

.. mc-cmd:: --version-id, vid
   :optional:

   .. include:: /includes/facts-versioning.rst
      :start-after: start-version-id-desc
      :end-before: end-version-id-desc

   与以下参数互斥：

   - :mc-cmd:`~mc tag remove --rewind`
   - :mc-cmd:`~mc tag remove --versions`

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

移除存储桶或对象上的标签
~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc:`mc tag remove` 移除存储桶或对象上的标签：

.. code-block:: shell
   :class: copyable

   mc tag remove ALIAS/PATH

- 将 :mc-cmd:`ALIAS <mc tag remove ALIAS>` 替换为 MinIO 部署的
  :ref:`alias <alias>`。

- 将 :mc-cmd:`PATH <mc tag remove ALIAS>` 替换为 MinIO 部署中存储桶
  或对象的路径。

行为
----

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
