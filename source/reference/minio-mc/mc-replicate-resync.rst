.. _minio-mc-replicate-resync:

=======================
``mc replicate resync``
=======================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc replicate reset
.. mc:: mc replicate resync

语法
----

.. start-mc-replicate-resync-desc

:mc:`mc replicate resync` 命令会将指定 MinIO 存储桶中的所有对象，
重新同步到远端 :ref:`replication
<minio-bucket-replication-serverside>` 目标。

.. end-mc-replicate-resync-desc

此命令*要求*先使用 :mc-cmd:`mc replicate add` 命令配置远端存储桶目标。
执行 :mc:`mc replicate resync` 时，必须指定由此生成的远端 ARN。

此命令支持使用 active-active 复制的远端作为“备份”来源来重建 MinIO 部署。
有关 active-active 复制的更多信息，请参阅以下教程：

- :ref:`minio-bucket-replication-serverside-twoway`
- :ref:`minio-bucket-replication-serverside-multi`

.. tab-set::

   .. tab-item:: 示例

      以下命令将 ``myminio`` MinIO 部署中 ``mydata`` 存储桶的内容，
      重新同步到与指定 ``--remote-bucket`` 关联的远端 MinIO 部署：

      .. code-block:: shell
         :class: copyable

         mc replicate resync start \
            --remote-bucket "arn:minio:replication::d3c086c7-1d64-40c2-954b-fe8222907033:mydata" \ 
            myminio/mydata

   .. tab-item:: 语法

      命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] replicate resync start|status  \
                          --remote-bucket "string"       \
                          [--older-than "string"]        \
                          ALIAS

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   MinIO 部署的 :ref:`alias <alias>`，以及 MinIO 用作复制源的存储桶或存储桶前缀的完整路径。
   例如，以下命令使用与 ``primary`` 别名关联的 MinIO 部署上的 ``data``
   存储桶启动复制。

   .. code-block:: none

      mc replicate resync start primary/data --remote-bucket "ARN"

.. mc-cmd:: start
   :required:

   使用指定的 :mc-cmd:`bucket <mc replicate resync ALIAS>` 作为源，
   并使用 :mc-cmd:`--remote-bucket <mc replicate resync --remote-bucket>` 作为远端目标，
   启动重新同步过程。

   与 :mc:`mc replicate resync status` 互斥。

.. mc-cmd:: status
   :required:

   返回指定 :mc-cmd:`bucket <mc replicate resync ALIAS>` 到所有远端目标的重新同步状态。

   包含 :mc-cmd:`~mc replicate resync --remote-bucket` 参数可将状态输出过滤为仅显示指定远端目标。

.. mc-cmd:: --remote-bucket
   :required:

   指定目标部署和存储桶的 ARN。
   
   可通过 :mc-cmd:`mc replicate ls` 配合 ``--json`` 选项获取 ARN。
   ``rule.Destination.Bucket`` 字段包含任意给定复制规则的 ARN。

.. mc-cmd:: older-than
   :optional:

   指定一个以天为单位的时长，MinIO 仅会重新同步早于该时长的对象。

   仅可与 :mc:`mc replicate resync start` 一起使用。

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

从源存储桶重新同步远端复制目标
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下 :mc:`mc replicate resync` 命令会将指定源存储桶中的所有对象重新同步到远端目标，
不考虑其复制状态：

.. code-block:: shell
   :class: copyable

   mc replicate resync start --remote-bucket "arn:minio:replication::UUID:data" primary/data

- 将 ``primary/data`` 替换为 :mc-cmd:`~mc replicate add ALIAS` 对应的完整存储桶路径，用于创建复制配置。

- 将 :mc-cmd:`~mc replicate add --remote-bucket` 的值替换为远端目标的 ARN。
  使用 :mc-cmd:`mc replicate ls` 列出所有已配置的远端复制目标。

行为
----

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
