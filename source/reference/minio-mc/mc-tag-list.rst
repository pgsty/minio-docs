.. _minio-mc-tag-list:

===============
``mc tag list``
===============

.. default-domain:: minio

.. contents:: Table of Contents
   :local:
   :depth: 2

.. mc:: mc tag list

.. |command| replace:: :mc:`mc tag list`
.. |rewind| replace:: :mc-cmd:`~mc tag list --rewind`
.. |versions| replace:: :mc-cmd:`~mc tag list --versions`
.. |versionid| replace:: :mc-cmd:`~mc tag list --version-id`
.. |alias| replace:: :mc-cmd:`~mc tag list ALIAS`

语法
----

.. start-mc-tag-list-desc

:mc:`mc tag list` 命令列出存储桶或对象上的所有标签。

.. end-mc-tag-list-desc

.. tab-set::

   .. tab-item:: 示例

      以下命令列出 ``myminio`` MinIO 部署中 ``mydata`` 存储桶的标签：

      .. code-block:: shell
         :class: copyable

         mc tag list myminio/mydata

   .. tab-item:: 语法

      该命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] tag set                   \
                          [--rewind "string"]       \
                          [--versions]              \
                          [--version-id "string"]*  \
                          ALIAS

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

      :mc-cmd:`mc tag list --version-id` 与多个参数互斥。更多信息请参见参考文档。

参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   MinIO 部署的 :ref:`alias <alias>`，以及要列出全部标签的对象完整路径
   （例如存储桶和对象路径）。例如：

   .. code-block:: none

      mc tag list myminio/mybucket/object.txt

.. mc-cmd:: --recursive, r
   :optional:

   .. versionadded:: RELEASE.2023-05-04T18-10-16Z

   递归列出 :mc:`ALIAS <mc tag list ALIAS>` 指定路径下所有对象的标签。

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

   将 :mc-cmd:`~mc tag list --versions` 与
   :mc-cmd:`~mc tag list --rewind` 组合使用，可列出特定时间点存在的
   所有对象版本的标签。

.. mc-cmd:: --version-id, vid
   :optional:

   .. include:: /includes/facts-versioning.rst
      :start-after: start-version-id-desc
      :end-before: end-version-id-desc

   与以下参数互斥：

   - :mc-cmd:`~mc tag list --rewind`
   - :mc-cmd:`~mc tag list --versions`

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

列出存储桶或对象的标签
~~~~~~~~~~~~~~~~~~~~~~

使用 :mc:`mc tag list` 列出存储桶或对象的标签：

.. code-block:: shell
   :class: copyable

   mc tag list ALIAS/PATH

- 将 :mc-cmd:`ALIAS <mc tag list ALIAS>` 替换为 MinIO 部署的
  :ref:`alias <alias>`。

- 将 :mc-cmd:`PATH <mc tag list ALIAS>` 替换为 MinIO 部署中存储桶或对象的路径。

行为
----

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
