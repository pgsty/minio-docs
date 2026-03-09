.. _minio-mc-replicate-rm:

===================
``mc replicate rm``
===================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc replicate remove
.. mc:: mc replicate rm

.. versionchanged:: RELEASE.2022-12-24T15-21-38Z 

   ``mc replicate rm`` 替代 ``mc admin bucket remote rm`` 命令。
   删除复制配置时会自动删除其底层远程目标。

语法
----

.. start-mc-replicate-rm-desc

:mc:`mc replicate rm` 命令用于从 MinIO 存储桶中删除
:ref:`replication rule <minio-bucket-replication-serverside>`。

.. end-mc-replicate-rm-desc

:mc:`mc replicate remove` 命令与 :mc:`mc replicate rm` 功能等价。

.. code-block:: shell

   mc [GLOBALFLAGS] replicate rm FLAGS [FLAGS] ALIAS

.. tab-set::

   .. tab-item:: 示例

      以下命令从 ``myminio`` MinIO 部署中的 ``mydata`` 存储桶删除指定
      id 的复制规则：

      .. code-block:: shell
         :class: copyable

         mc replicate rm --id "c76um9h4b0t1ijr36mug" myminio/mydata

   .. tab-item:: 语法

      命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] replicate rm     \
                          --id "string"    \
                          [--all --force]  \
                          ALIAS

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~

.. mc-cmd:: ALIAS

   *必需* MinIO 部署的 :ref:`alias <alias>`，以及要删除复制规则的
   存储桶或存储桶前缀的完整路径。例如：

   .. code-block:: none

      mc replicate rm --id "ID" myminio/mybucket


.. mc-cmd:: --id
   

   *必需* 指定已配置复制规则的唯一 ID。

   如果指定 :mc-cmd:`~mc replicate rm --all`，则可省略此选项

.. mc-cmd:: --all
   

   *可选* 删除指定存储桶上的所有复制规则。要求同时指定
   :mc-cmd:`~mc replicate rm --force` 标志。

.. mc-cmd:: --force
   

   *可选* 当指定 :mc-cmd:`~mc replicate rm --all` 时必需。


全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

从存储桶中删除一条复制规则
~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc:`mc replicate rm` 删除存储桶复制规则：

.. code-block:: shell
   :class: copyable

   mc replicate rm --id "ID" ALIAS/PATH

- 将 :mc-cmd:`ID <mc replicate rm --id>` 替换为要删除的复制规则唯一 ID。
  使用 :mc:`mc replicate ls` 列出该存储桶的所有复制规则。

- 将 :mc-cmd:`ALIAS <mc replicate rm ALIAS>` 替换为 MinIO 部署的
  :mc:`alias <mc alias>`。

- 将 :mc-cmd:`PATH <mc replicate rm ALIAS>` 替换为存储桶或存储桶前缀的路径。

从存储桶中删除所有复制规则
~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc:`mc replicate rm` 删除存储桶复制规则：

.. code-block:: shell
   :class: copyable

   mc replicate rm --all --force ALIAS/PATH

- 将 :mc-cmd:`ALIAS <mc replicate rm ALIAS>` 替换为 MinIO 部署的
  :mc:`alias <mc alias>`。

- 将 :mc-cmd:`PATH <mc replicate rm ALIAS>` 替换为存储桶或存储桶前缀的路径。

行为
----

删除复制规则不会影响已复制对象
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

删除存储桶的一条或全部复制规则，*不会*
删除已按这些规则复制的任何对象。

使用此命令或 :mc:`mc rb` 命令可删除远程目标上的已复制对象。
你可以通过 ``X-Amz-Replication-Status`` 元数据字段识别已复制对象，其值为
``REPLICA``。包含来自多个复制源对象的存储桶在删除前可能需要额外的
处理与过滤，以确定对象来源。

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
