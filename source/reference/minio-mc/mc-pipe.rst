===========
``mc pipe``
===========

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc pipe

语法
----

.. start-mc-pipe-desc

:mc:`mc pipe` 命令将内容从 `STDIN <https://www.gnu.org/software/libc/manual/html_node/Standard-Streams.html>`__ 流式传输到目标对象。

.. end-mc-pipe-desc

.. tab-set::

   .. tab-item:: EXAMPLE

      以下命令将 ``STDIN`` 的内容写入 S3 兼容存储。

      .. code-block:: shell
         :class: copyable

         echo "My Meeting Notes" | mc pipe s3/engineering/meeting-notes.txt

   .. tab-item:: SYNTAX

      该命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] pipe                              \
                          TARGET                            \
                          [--attr "string"]                 \
                          [--checksum "string"]             \
                          [--enc-kms "string"]              \
                          [--enc-s3 "string"]               \
                          [--enc-c "string"]                \
                          [--storage-class, --sc "string"]  \
                          [--tags "string"] 

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

.. versionchanged:: RELEASE.2023-01-11T03-14-16Z

   ``mc pipe`` 现已支持并发上传，以提升大流式数据的吞吐量。

参数
~~~~

.. mc-cmd:: TARGET
   :required:

   命令应运行的 :ref:`alias <minio-mc-alias>` 或前缀完整路径。

.. mc-cmd:: --attr
   :optional:

   为对象添加自定义元数据。

   将键值对指定为 ``KEY=VALUE\;``，每对之间用反斜杠加分号（``\;``）分隔。
   例如：``--attr key1=value1\;key2=value2\;key3=value3``。

.. mc-cmd:: --checksum
   :optional:

   .. versionadded:: RELEASE.2024-10-02T08-27-28Z

   为上传的对象添加校验和。
   
   有效值为：
   - ``MD5``
   - ``CRC32``
   - ``CRC32C``
   - ``SHA1``
   - ``SHA256``

   该标志依赖服务器 trailing headers，并可用于 AWS 或 MinIO 目标。

.. block include of enc-c , enc-s3, and enc-kms

.. include:: /includes/common-minio-sse.rst
   :start-after: start-minio-mc-sse-options
   :end-before: end-minio-mc-sse-options


.. mc-cmd:: --storage-class, --sc
   :optional:

   为 :mc-cmd:`~mc pipe TARGET` 处的新对象设置存储类别。
         
   关于 S3 存储类别的更多信息，请参见 :aws-docs:`Amazon 文档 <AmazonS3/latest/dev/storage-class-intro.html>`。

.. mc-cmd:: --tags
   :optional:

   为 TARGET 应用一个或多个标签。

   将键值对列表指定为 ``KEY1=VALUE1&KEY2=VALUE2``，其中每一对表示分配给对象的一个标签。

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

将 ``STDIN`` 内容写入本地文件系统
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下命令将 STDIN 的内容写入本地文件系统中的 ``/tmp`` 文件夹。

.. code-block:: shell
   :class: copyable

   mc pipe /tmp/hello-world.go

将 ISO 镜像复制到 S3 存储
~~~~~~~~~~~~~~~~~~~~~~~~~

以下命令先流式读取 Debian 的 ISO 镜像内容，然后使用该数据流在 S3 路径创建对象。

.. code-block:: shell
   :class: copyable

   cat debian-live-11.5.0-amd64-mate.iso | mc pipe s3/opensource-isos/debian-11-5.iso

将 MySQL 数据库转储流式传输到 S3
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下命令先流式传输 MySQL 数据库，再通过 :mc-cmd:`mc pipe` 使用该数据流在 S3 上创建备份：

.. code-block:: shell
   :class: copyable

   mysqldump -u root -p ******* accountsdb | mc pipe s3/sql-backups/backups/accountsdb-sep-28-2022.sql

将文件写入 Reduced Redundancy 存储类别
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下命令读取 STDIN 数据流，并在 S3 的 Reduced Redundancy 存储类别中创建对象。

.. code-block:: shell
   :class: copyable

    mc pipe --storage-class REDUCED_REDUNDANCY s3/personalbuck/meeting-notes.txt

将文件连同元数据复制到 MinIO 部署
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下命令将 MP3 文件上传到 ALIAS 为 ``myminio``、存储桶为 ``music`` 的 MinIO 部署。
写入对象时附带 ``Cache-Control`` 和 ``Artist`` 元数据。

.. code-block:: shell
   :class: copyable

   cat music.mp3 | mc pipe --attr "Cache-Control=max-age=90000,min-fresh=9000;Artist=Unknown" myminio/music/guitar.mp3

为上传对象设置标签
~~~~~~~~~~~~~~~~~

以下命令在 ALIAS 为 ``myminio`` 的 MinIO 部署中，于 ``mybucket`` 存储桶创建一个带两个标签的对象。
MinIO 支持为对象最多添加 10 个自定义标签。

.. code-block:: shell
   :class: copyable

   tar cvf - . | mc pipe --tags "category=prod&type=backup" myminio/mybucket/backup.tar
