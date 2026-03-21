===========
``mc stat``
===========

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc stat

.. |command| replace:: :mc:`mc stat`
.. |rewind| replace:: :mc-cmd:`~mc stat --rewind`
.. |versions| replace:: :mc-cmd:`~mc stat --versions`
.. |versionid| replace:: :mc-cmd:`~mc stat --version-id`
.. |alias| replace:: :mc-cmd:`~mc stat ALIAS`

语法
-----------

.. start-mc-stat-desc

:mc:`mc stat` 命令用于显示 MinIO 存储桶中对象的信息，包括对象元数据。
你也可以使用它来检索存储桶元数据。

.. end-mc-stat-desc

你可以对本地文件系统使用 :mc:`mc stat`，以获得与 ``stat`` 命令行工具类似的结果。

.. tab-set::

   .. tab-item:: 示例

      以下命令显示 ``myminio`` MinIO 部署中 ``mydata`` 存储桶内所有对象的信息：

      .. code-block:: shell
         :class: copyable

         mc stat --recursive myminio/mydata

   .. tab-item:: 语法

      该命令具有以下语法：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] stat                      \
                          [--enc-c "value"]         \
                          [--no-list]               \
                          [--recursive]             \
                          [--rewind "string"]       \
                          [--versions]              \
                          [--version-id "string"]*  \
                          ALIAS [ALIAS ...]


      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

      :mc-cmd:`mc stat --version-id` 与多个参数互斥。更多信息请参阅参考文档。

参数
~~~~~~~~~~

.. mc-cmd:: ALIAS
   :required:

   MinIO 部署的 :ref:`alias <alias>`，以及要检索详细信息的对象完整路径。例如：

   .. code-block:: shell

      mc stat myminio/mybucket/myobject.txt

   你可以在同一个或不同的 MinIO 部署上指定多个对象：

   .. code-block:: shell

      mc stat myminio/mybucket/myobject.txt myminio/mybucket/myobject.txt

   如果指定的是存储桶路径或存储桶前缀路径，则**必须**包含 :mc-cmd:`mc stat --recursive` 标志：

   .. code-block:: shell

      mc stat --recursive myminio/mybucket/

   要检索本地文件系统中文件的信息，请指定该文件的完整路径：

   .. code-block:: shell

      mc stat ~/data/myobject.txt

.. block include of enc-c

.. include:: /includes/common-minio-sse.rst
   :start-after: start-minio-mc-sse-c-only
   :end-before: end-minio-mc-sse-options

.. mc-cmd:: --no-list
   :optional:

   如果目标不存在，则禁用所有 ``LIST`` 操作。

.. mc-cmd:: --recursive, r
   :optional:

   递归地对 :mc-cmd:`~mc stat ALIAS` 指定的 MinIO 存储桶内容执行 :mc:`mc stat`。

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

   将 :mc-cmd:`~mc stat --versions` 与 :mc-cmd:`~mc stat --rewind` 一起使用，可移除某个特定时间点存在的所有对象版本。

.. mc-cmd:: --version-id, vid
   :optional:

   .. include:: /includes/facts-versioning.rst
      :start-after: start-version-id-desc
      :end-before: end-version-id-desc

   与以下任一标志互斥：

   - :mc-cmd:`~mc stat --versions`
   - :mc-cmd:`~mc stat --rewind`
   - :mc-cmd:`~mc stat --recursive`

全局标志
~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
--------

显示对象详情
~~~~~~~~~~~~~~~~~~~~~~

以下示例显示存储桶 ``mybucket`` 中对象 ``myfile.txt`` 的详细信息：

.. code-block:: shell
   :class: copyable

   mc stat myminio/mybucket/myfile.txt

输出类似如下：

.. code-block:: shell

   Name      : myfile.txt
   Date      : 2024-07-16 15:40:02 MDT 
   Size      : 6.0 KiB 
   ETag      : 3b38f7b05a0c42acdc377e60b2a74ddf 
   Type      : file 
   Metadata  :
     Content-Type: text/plain 

你可以通过添加多个路径来指定多个对象：

.. code-block:: shell
   :class: copyable

   mc stat myminio/mybucket/file1.txt myminio/yourbucket/file2.txt

要显示存储桶中所有对象的详细信息，请使用 :mc-cmd:`~mc stat --recursive`。
以下示例显示存储桶 ``mybucket`` 中所有对象的详细信息：

.. code-block:: shell
   :class: copyable

   mc stat --recursive myminio/mybucket

输出类似如下：

.. code-block:: shell

   Name      : file1.txt
   Date      : 2024-07-16 15:40:02 MDT
   Size      : 6.0 KiB
   ETag      : 3b38f7b05a0c42acdc377e60b2a74ddf
   Type      : file
   Metadata  :
     Content-Type: text/plain

   Name      : file2.txt
   Date      : 2024-07-26 10:45:19 MDT
   Size      : 6.0 KiB
   ETag      : 3b38f7b05a0c42acdc377e60b2a74ddf
   Type      : file
   Metadata  :
     Content-Type: text/plain


显示存储桶详情
~~~~~~~~~~~~~~~~~~~~~~

以下示例显示 ``myminio`` MinIO 部署上存储桶 ``mybucket`` 的信息：

.. code-block:: shell
   :class: copyable

   mc stat myminio/mybucket

输出类似如下：

.. code-block:: shell

   Name      : mybucket
   Date      : 2024-07-26 10:56:43 MDT 
   Size      : N/A    
   Type      : folder 

   Properties:
     Versioning: Un-versioned
     Location: us-east-1
     Anonymous: Disabled
     ILM: Disabled

   Usage:
         Total size: 6.0 KiB
      Objects count: 1
     Versions count: 0

   Object sizes histogram:
      1 object(s) BETWEEN_1024B_AND_1_MB
      1 object(s) BETWEEN_1024_B_AND_64_KB
      0 object(s) BETWEEN_10_MB_AND_64_MB
      0 object(s) BETWEEN_128_MB_AND_512_MB
      0 object(s) BETWEEN_1_MB_AND_10_MB
      0 object(s) BETWEEN_256_KB_AND_512_KB
      0 object(s) BETWEEN_512_KB_AND_1_MB
      0 object(s) BETWEEN_64_KB_AND_256_KB
      0 object(s) BETWEEN_64_MB_AND_128_MB
      0 object(s) GREATER_THAN_512_MB
      0 object(s) LESS_THAN_1024_B


存储桶中的对象数量
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

要显示存储桶中的对象数量，请使用 :std:option:`--json <mc.--json>` 并通过 JSON 解析器提取 ``objectsCount`` 的值：

以下示例使用 `jq <https://jqlang.github.io/jq/>`__ 工具：

.. code-block:: shell
   :class: copyable

   mc stat myminio/mybucket --json | jq '.Usage.objectsCount'


行为
--------

S3 兼容性
~~~~~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
