.. _minio-mc-batch-status:

===================
``mc batch status``
===================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc batch status

.. versionchanged:: MinIO RELEASE.2022-10-08T20-11-00Z or later

语法
----

.. start-mc-batch-status-desc

:mc:`mc batch status` 命令会输出 MinIO 服务器上作业事件的汇总信息。

.. versionchanged:: mc RELEASE.2024-07-03T20-17-25Z

   Batch status 会显示活动且正在进行的作业，或前 3（三）天内已完成的任意批处理作业的汇总信息。

.. end-mc-batch-status-desc


.. tab-set::

   .. tab-item:: 示例

      以下命令会输出 ``myminio`` alias 上当前正在运行、JobID 为 ``KwSysDpxcBU9FNhGkn2dCf`` 的指定作业状态。

      .. code-block:: shell
         :class: copyable

         mc batch status myminio "KwSysDpxcBU9FNhGkn2dCf"

   .. tab-item:: 语法

      该命令的语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] batch list TARGET           \
                                     ["JOBID"]

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~

.. mc-cmd:: TARGET
   :required:
   
   要显示批处理作业状态的 :ref:`alias <alias>`。

.. mc-cmd:: JOBID
   :optional:

   要汇总的作业的唯一标识符。
   要查找作业 ID，请使用 :mc:`mc batch list`。
   
   如果未指定，则该命令返回当前活动的批处理作业的汇总信息。

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

汇总活动复制作业的事件
~~~~~~~~~~~~~~~~~~~~~~

以下命令会为 :mc:`alias <mc alias>` ``myminio`` 对应部署上的活动作业提供实时汇总：

.. code-block:: shell
   :class: copyable

   mc batch status myminio "KwSysDpxcBU9FNhGkn2dCf"

- 将 ``myminio`` 替换为应运行该作业的 MinIO 部署的 :mc:`alias <mc alias>`。

上述命令的输出如下所示：

.. code-block:: shell

   ●∙∙
   JobType:        replicate
   Objects:        28766
   Versions:       28766
   FailedObjects:  0
   Transferred:    406 MiB
   Elapsed:        2m14.227222868s
   CurrObjName:    share/doc/xml-core/examples/foo.xmlcatalogs
 


S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
