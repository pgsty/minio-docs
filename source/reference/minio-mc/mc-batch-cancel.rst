.. _minio-mc-batch-cancel:

===================
``mc batch cancel``
===================

.. default-domain:: minio

.. contents:: Table of Contents
   :local:
   :depth: 2

.. mc:: mc batch cancel

.. versionadded:: mc RELEASE.2023-03-20T17-17-53Z

语法
----

.. start-mc-batch-cancel-desc

:mc:`mc batch cancel` 可停止正在进行的批处理作业。

.. end-mc-batch-cancel-desc

您必须指定作业 ID。
要查找作业 ID，请使用 :mc-cmd:`mc batch list`。

.. tab-set::

   .. tab-item:: 示例

      以下命令会输出标识为 ``KwSysDpxcBU9FNhGkn2dCf`` 的作业定义。

      .. code-block:: shell
         :class: copyable

         mc batch cancel myminio KwSysDpxcBU9FNhGkn2dCf

   .. tab-item:: 语法

      该命令使用以下语法：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] batch cancel ALIAS JOBID

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~

.. mc-cmd:: ALIAS
   :required:
   
   当前运行该作业的 MinIO 部署的 :ref:`alias <alias>`。

.. mc-cmd:: JOBID
   :required:

   要取消的批处理作业的唯一标识符。
   要查找作业 ID，请使用 :mc:`mc batch list`。
   
全局参数
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

取消正在进行的批处理作业
~~~~~~~~~~~~~~~~~~~~~~~~

以下命令会取消别名为 ``myminio`` 的部署上 ID 为 ``KwSysDpxcBU9FNhGkn2dCf`` 的作业：

.. code-block:: shell
   :class: copyable

   mc batch cancel myminio KwSysDpxcBU9FNhGkn2dCf

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
