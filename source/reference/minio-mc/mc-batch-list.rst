.. _minio-mc-batch-list:

=================
``mc batch list``
=================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc batch list

.. versionchanged:: MinIO RELEASE.2022-10-08T20-11-00Z or later

语法
----

.. start-mc-batch-list-desc

:mc:`mc batch list` 命令会输出部署中当前正在进行的批处理作业列表。

.. end-mc-batch-list-desc


.. tab-set::

   .. tab-item:: 示例

      以下命令会输出 ``myminio`` alias 上当前正在进行的所有作业列表。

      .. code-block:: shell
         :class: copyable

         mc batch list myminio

   .. tab-item:: 语法

      该命令的语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] batch list TARGET           \
                                     --type "string"

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~

.. mc-cmd:: TARGET
   :required:
   
   要列出进行中作业的目标部署的 :ref:`alias <alias>`。
   
.. mc-cmd:: --type
   :optional:

   仅列出指定类型的批处理作业。
   
全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

列出所有 ``replicate`` 类型的批处理作业
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下命令列出 :mc:`alias <mc alias>` ``myminio`` 所对应部署上的 ``replicate``` 类型作业：

.. code-block:: shell
   :class: copyable

   mc batch list myminio --type "replicate"

- 将 ``myminio`` 替换为应运行该作业的 MinIO 部署的 :mc:`alias <mc alias>`。

- 将 ``replicate`` 替换为要输出的作业类型。
  
  当前，:mc:`mc batch` 仅支持 ``replicate`` 作业类型。

上述命令的输出类似如下：

.. code-block:: shell

   ID                      TYPE            USER            STARTED
   E24HH4nNMcgY5taynaPfxu  replicate       minioadmin      1 minute ago
 
S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility

权限
----

你必须具有 :policy-action:`admin:ListBatchJobs` 权限，才能列出该部署上的作业。 
