.. _minio-mc-tag-set:

==============
``mc tag set``
==============

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc tag set

.. |command| replace:: :mc:`mc tag set`
.. |rewind| replace:: :mc-cmd:`~mc tag set --rewind`
.. |versions| replace:: :mc-cmd:`~mc tag set --versions`
.. |versionid| replace:: :mc-cmd:`~mc tag set --version-id`
.. |alias| replace:: :mc-cmd:`~mc tag set ALIAS`

语法
----

.. start-mc-tag-set-desc

:mc:`mc tag set` 命令可为存储桶或对象设置一个或多个标签。

.. end-mc-tag-set-desc

MinIO 支持为对象最多添加 10 个自定义标签。

.. tab-set::

   .. tab-item:: 示例

      以下命令为 ``myminio`` MinIO 部署中的 ``mydata`` 存储桶设置标签：

      .. code-block:: shell
         :class: copyable

         mc tag set myminio/mydata "tag1=value1&tag2=value2"

   .. tab-item:: 语法

      该命令的语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] tag set                   \
                          [--rewind "string"]       \
                          [--versions]              \
                          [--version-id "string"]*  \
                          ALIAS                     \
                          "TAGS"

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

      :mc-cmd:`mc tag set --version-id` 与多个参数互斥。更多信息请参阅参考文档。


参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   MinIO 部署的 :ref:`alias <alias>`，以及要应用标签的对象完整路径（例如存储桶和对象路径）。例如：

   .. code-block:: none

      mc tag set myminio/mybucket/object.txt

.. mc-cmd:: TAGS
   :required:

   使用与号（``&``）分隔的键值对（``KEY=VALUE``）列表，其中每一对表示要分配给对象的一个标签。例如：

   .. code-block:: none

      mc tag set myminio/mybucket/object.txt "key1=value1&key2=value2"

.. mc-cmd:: --exclude-folders
   :optional:

   .. versionadded:: RELEASE.2024-01-11T05-49-32Z

   与 :mc-cmd:`~mc tag set --recursive` 一起使用时，:mc:`mc tag set` 将**不会**遍历子前缀。
   标签仅应用于指定路径下的对象。
   需要 :mc-cmd:`~mc tag set --recursive`。

   以下示例将 ``destination=international`` 标签应用到 ``vacation-photos/cancun/`` 下的对象，但不应用到 ``vacation-photos/cancun/ocean/`` 或其他前缀。
   
   例如，上述命令会将标签添加到 ``vacation-photos/cancun/pretty-beach.jpg``，但不会添加到 ``vacation-photos/cancun/ocean/tropical-fish.jpg``。

   .. code-block:: shell

      mc tag set myminio/vacation-photos/cancun "destination=international" --exclude-folders --recursive
   
.. mc-cmd:: --recursive, r
   :optional:

   .. versionadded:: RELEASE.2023-05-04T18-10-16Z

   递归地将标签应用到 :mc:`ALIAS <mc tag set ALIAS>` 指定路径下的所有对象。

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

   将 :mc-cmd:`~mc tag set --versions` 与
   :mc-cmd:`~mc tag set --rewind` 一起使用，可将标签应用到某个特定时间点存在的所有对象版本。

.. mc-cmd:: --version-id, --vid
   :optional:

   .. include:: /includes/facts-versioning.rst
      :start-after: start-version-id-desc
      :end-before: end-version-id-desc

   与以下参数互斥：

   - :mc-cmd:`~mc tag set --rewind`
   - :mc-cmd:`~mc tag set --versions`

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

将标签应用到存储桶或对象
~~~~~~~~~~~~~~~~~~~~~~

使用 :mc:`mc tag set` 将标签应用到存储桶或对象：

.. code-block:: shell
   :class: copyable

   mc tag set ALIAS/PATH "TAGS"

- 将 :mc-cmd:`ALIAS <mc tag set ALIAS>` 替换为 MinIO 部署的
  :ref:`alias <alias>`。

- 将 :mc-cmd:`PATH <mc tag set ALIAS>` 替换为 MinIO 部署中存储桶
  或对象的路径。

- 将 :mc-cmd:`TAGS <mc tag set TAGS>` 替换为一个或多个用与号分隔（``&``）的键值对，
  每个键值对表示一个标签及其对应值。

行为
----

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
