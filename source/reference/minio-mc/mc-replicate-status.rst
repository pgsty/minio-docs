.. _minio-mc-replicate-status:

=======================
``mc replicate status``
=======================

.. default-domain:: minio

.. contents:: Table of Contents
   :local:
   :depth: 2

.. mc:: mc replicate status

语法
----

.. start-mc-replicate-status-desc

:mc:`mc replicate status` 命令显示 MinIO 存储桶的 :ref:`复制状态 <minio-bucket-replication-serverside>`。
该状态还会列出远程目标路径或位置。

.. end-mc-replicate-status-desc

.. tab-set::

   .. tab-item:: 示例

      以下命令显示 ``myminio`` MinIO 部署上 ``mydata`` 存储桶的当前复制状态：

      .. code-block:: shell
         :class: copyable

         mc replicate status myminio/mydata

   .. tab-item:: 语法

      该命令具有以下语法：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] replicate status TARGET
                                    [--limit-upload value]
                                    [--limit-download value]

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   MinIO 部署的 :ref:`alias <alias>`，以及要显示复制状态的存储桶或存储桶前缀的完整路径。
   例如：

   .. code-block:: none

      mc replicate status myminio/mybucket

.. mc-cmd:: --limit-download
   :optional:

   将下载速率限制为不超过指定值，单位可为 KiB/s、MiB/s 或 GiB/s。
   有效单位包括：
   
   - ``B`` 表示 bytes
   - ``K`` 表示 kilobytes
   - ``G`` 表示 gigabytes
   - ``T`` 表示 terabytes
   - ``Ki`` 表示 kibibytes
   - ``Gi`` 表示 gibibytes
   - ``Ti`` 表示 tebibytes

   例如，要将下载速率限制为不超过 1 GiB/s，可使用以下参数：

   .. code-block::

      --limit-download 1G

   如果未指定，MinIO 使用不限速的下载速率。

.. mc-cmd:: --limit-upload
   :optional:

   将上传速率限制为不超过指定值，单位可为 KiB/s、MiB/s 或 GiB/s。
   有效单位包括：
   
   - ``B`` 表示 bytes
   - ``K`` 表示 kilobytes
   - ``G`` 表示 gigabytes
   - ``T`` 表示 terabytes
   - ``Ki`` 表示 kibibytes
   - ``Gi`` 表示 gibibytes
   - ``Ti`` 表示 tebibytes

   例如，要将上传速率限制为不超过 1 GiB/s，可使用以下参数：

   .. code-block::

      --limit-upload 1G

   如果未指定，MinIO 使用不限速的上传速率。


全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

显示复制状态
~~~~~~~~~~~~

使用 :mc:`mc replicate status` 显示存储桶复制状态：

.. code-block:: shell
   :class: copyable

   mc replicate status ALIAS/PATH

- 将 :mc-cmd:`ALIAS <mc replicate status ALIAS>` 替换为 MinIO 部署的 :mc:`alias <mc alias>`。

- 将 :mc-cmd:`PATH <mc replicate status ALIAS>` 替换为存储桶或存储桶前缀路径。

行为
----

移除并重新添加的 ARN
~~~~~~~~~~~~~~~~~~~

.. versionchanged:: mc RELEASE.2023-03-20T17-17-53Z

该命令的标准输出不会显示此前已从复制配置中移除的 ARN。

如需列出所有 ARN（包括不再属于当前复制配置的 ARN），请使用 ``--json`` 标志。
``json`` 输出会持续显示在旧 ARN 下复制的数据。
如果某个 ARN 在同一存储桶上被移除后又重新添加，这些信息会很有价值。

新的 ARN **不会**触发对先前已同步对象的重新复制。
