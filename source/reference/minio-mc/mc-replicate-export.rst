.. _minio-mc-replicate-export:

=======================
``mc replicate export``
=======================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc replicate export

语法
----

.. start-mc-replicate-export-desc

:mc:`mc replicate export` 命令将 MinIO 存储桶的 JSON 格式
:ref:`复制规则 <minio-bucket-replication-serverside>` 导出到 ``STDOUT``。

.. end-mc-replicate-export-desc

.. tab-set::

   .. tab-item:: 示例

      以下命令导出 ``myminio`` MinIO 部署中 ``mydata`` 存储桶的复制配置：

      .. code-block:: shell
         :class: copyable

         mc replicate export myminio/mydata > mydata-replication.json

   .. tab-item:: 语法

      该命令的语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] export ALIAS

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~

.. mc-cmd:: ALIAS

   *必填* MinIO 部署的 :ref:`alias <alias>`，以及要导出复制规则的存储桶或
   存储桶前缀的完整路径。例如：

   .. code-block:: none

      mc replicate export myminio/mybucket

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

导出现有复制规则
~~~~~~~~~~~~~~~~

使用 :mc:`mc replicate export` 导出存储桶复制规则：

.. code-block:: shell
   :class: copyable

   mc replicate export ALIAS/PATH > bucket-replication-rules.json

- 将 :mc-cmd:`ALIAS <mc replicate export ALIAS>` 替换为 MinIO 部署的
  :mc:`alias <mc alias>`。

- 将 :mc-cmd:`PATH <mc replicate export ALIAS>` 替换为存储桶或存储桶前缀的
  路径。

行为
----

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
