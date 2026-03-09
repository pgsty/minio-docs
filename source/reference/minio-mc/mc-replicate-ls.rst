.. _minio-mc-replicate-ls:

===================
``mc replicate ls``
===================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc replicate list
.. mc:: mc replicate ls

.. versionchanged:: RELEASE.2022-12-24T15-21-38Z 

   ``mc replicate ls`` 替代 ``mc admin bucket remote ls`` 命令。

语法
----

.. start-mc-replicate-ls-desc

:mc:`mc replicate ls` 命令列出 MinIO 存储桶上的所有
:ref:`复制规则 <minio-bucket-replication-serverside>`。

.. end-mc-replicate-ls-desc

:mc:`mc replicate list` 命令与 :mc:`mc replicate ls` 功能等价。

.. tab-set::

   .. tab-item:: 示例

      以下命令列出 ``myminio`` MinIO 部署中 ``mydata``
      存储桶的所有已启用复制规则：

      .. code-block:: shell
         :class: copyable

         mc replicate ls --status "enabled" myminio/mydata

   .. tab-item:: 语法

      该命令的语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] replicate ls         \
                          [--status "string"]  \
                          ALIAS

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   MinIO 部署的 :ref:`alias <alias>`，以及要列出复制规则的
   存储桶或存储桶前缀的完整路径。例如：

   .. code-block:: none

      mc replicate ls myminio/mybucket


.. mc-cmd:: --status
   :optional:

   按状态筛选存储桶上的复制规则。
   指定以下值之一：

   - ``enabled`` - 仅显示已启用的复制规则。
   - ``disabled`` - 仅显示已禁用的复制规则。

   如果省略，:mc:`mc replicate ls` 默认显示所有复制规则。

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

列出现有复制规则
~~~~~~~~~~~~~~~~

使用 :mc:`mc replicate ls` 列出存储桶复制规则：

.. code-block:: shell
   :class: copyable

   mc replicate ls ALIAS/PATH

- 将 :mc-cmd:`ALIAS <mc replicate ls ALIAS>` 替换为 MinIO 部署的
  :mc:`alias <mc alias>`。

- 将 :mc-cmd:`PATH <mc replicate ls ALIAS>` 替换为存储桶或存储桶前缀的路径。

行为
----

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
