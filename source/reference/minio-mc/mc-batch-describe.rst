.. _minio-mc-batch-describe:

=====================
``mc batch describe``
=====================

.. default-domain:: minio

.. contents:: Table of Contents
   :local:
   :depth: 2

.. mc:: mc batch describe

.. versionchanged:: MinIO RELEASE.2022-10-08T20-11-00Z or later

语法
----

.. start-mc-batch-describe-desc

:mc:`mc batch describe` 命令会输出指定作业 ID 的作业定义。

.. end-mc-batch-describe-desc

必须指定作业 ID。
要查找作业 ID，请使用 :mc-cmd:`mc batch list`。

.. tab-set::

   .. tab-item:: 示例

      以下命令会输出标识为 ``KwSysDpxcBU9FNhGkn2dCf`` 的作业定义。

      .. code-block:: shell
         :class: copyable

         mc batch describe myminio KwSysDpxcBU9FNhGkn2dCf

   .. tab-item:: 语法

      此命令的语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] batch describe TARGET           \
                                         JOBID

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~

.. mc-cmd:: TARGET
   :required:
   
   用于查找作业 ID 的 MinIO 部署 :ref:`alias <alias>`。

.. mc-cmd:: JOBID
   :required:

   要描述的作业的唯一标识符。
   要查找作业 ID，请使用 :mc:`mc batch list`。
   
全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

显示进行中批处理作业的定义
~~~~~~~~~~~~~~~~~~~~~~~~~~

以下命令会输出 :mc:`alias <mc alias>` ``myminio`` 上特定作业的完整作业定义：

.. code-block:: shell
   :class: copyable

   mc batch describe myminio KwSysDpxcBU9FNhGkn2dCf

- 将 ``myminio`` 替换为应运行该作业的 MinIO 部署的 :mc:`alias <mc alias>`。
- 将 ``KwSysDpxcBU9FNhGkn2dCf`` 替换为要定义的作业 ID。

上述命令的输出类似如下：

.. code-block:: shell

   mc batch describe myminio KwSysDpxcBU9FNhGkn2dCf
   replicate:
     apiVersion: v1
   ...
 
注意，此示例已截断。
输出结果是指定作业的完整作业定义。

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility

权限
----

你必须具有 :policy-action:`admin:DescribeBatchJobs` 权限，才能描述该部署上的作业。 
