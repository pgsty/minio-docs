.. _minio-mc-batch-start:

==================
``mc batch start``
==================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc batch start

.. versionchanged:: MinIO RELEASE.2022-10-08T20-11-00Z or later

语法
----

.. start-mc-batch-start-desc

:mc:`mc batch start` 命令根据批处理作业 YAML 文件启动一个批处理作业。

.. end-mc-batch-start-desc

批处理作业会运行一次直到完成（或达到文件中指定的最大重试次数）。
若要在完成后再次运行该批处理作业，必须重新启动它。

.. tab-set::

   .. tab-item:: 示例

      以下命令会在 ``myminio`` 别名的 ``mybucket`` 存储桶上，为 replicate 作业创建一个基础 YAML 文件。

      .. code-block:: shell
         :class: copyable

         mc batch start myminio jobfile.yaml

      上述命令的输出类似如下：

      .. code-block:: shell
         
         Successfully start 'replicate' job `B34HHqnNMcg1taynaPfxu` on '2022-10-24 17:19:06.296974771 -0700 PDT'

   .. tab-item:: 语法

      该命令具有以下语法：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] batch start    \
                                ALIAS   \
                                JOBFILE

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~

.. mc-cmd:: ALIAS
   :required:
   
   用于启动批处理作业的 :ref:`alias <alias>`。
   
   例如：

   .. code-block:: none

      mc batch start myminio replicate.yaml

.. mc-cmd:: JOBFILE
   :required:
   
   YAML 定义的批处理作业。
   作业可按需包含任意数量的任务；没有预定义的限制。
   
全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

启动批处理作业
~~~~~~~~~~~~~~

以下命令会在 :mc:`alias <mc alias>` ``myminio`` 对应的部署上，启动 ``replication.yaml`` 文件中定义的一批作业：

.. code-block:: shell
   :class: copyable

   mc batch start myminio ./replication.yaml

- 将 ``myminio`` 替换为应运行该作业的 MinIO 部署 :mc:`alias <mc alias>`。

- 将 ``./replication.yaml`` 替换为描述批处理作业的 YAML 格式文件。
  使用相对于当前位置的文件路径。

上述命令的输出类似如下：

.. code-block:: shell
   
   Successfully start 'replicate' job `E24HH4nNMcgY5taynaPfxu` on '2022-09-26 17:19:06.296974771 -0700 PDT'
 
S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility


权限
----

要启动作业，必须在该部署上具有 :policy-action:`admin:StartBatchJob` 权限。 
