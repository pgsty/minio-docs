.. _minio-mc-ilm-rule-rm:

==================
``mc ilm rule rm``
==================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc ilm rule rm

.. versionchanged:: RELEASE.2022-12-24T15-21-38Z

   ``mc ilm rule rm`` 替代 ``mc ilm rm``。

语法
------

.. start-mc-ilm-rule-rm-desc

:mc:`mc ilm rule rm` 命令用于从 MinIO 存储桶中删除一条对象生命周期管理规则。

.. end-mc-ilm-rule-rm-desc

.. tab-set::

   .. tab-item:: 示例

      以下命令从 ``myminio`` MinIO 部署的 ``mydata`` 存储桶中删除一条生命周期管理规则：

      .. code-block:: shell
         :class: copyable

         mc ilm rule rm --id "bgrt1ghju" myminio/mydata

   .. tab-item:: 语法

      该命令的语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] ilm rule rm                         \
                              --id "string" | (--all --force) \
                              ALIAS                           \

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~~~~~~~

.. mc-cmd:: ALIAS
   :required:
   
   要删除对象生命周期管理规则的 MinIO 部署上存储桶的 :ref:`alias <alias>` 和完整路径。
   例如：

   .. code-block:: none

      mc ilm rule rm myminio/mydata

.. mc-cmd:: --all
   :optional:

   删除存储桶中的所有规则。
   需要同时包含 :mc-cmd:`~mc ilm rule rm --force`。

   与 :mc:`~mc ilm rule rm --id` 互斥。

.. mc-cmd:: --force
   :optional:

   指定 :mc-cmd:`~mc ilm rule rm --all` 时必需。

.. mc-cmd:: --id
   :optional:

   规则的唯一 ID。
   使用 :mc:`mc ilm rule ls` 列出存储桶规则，并获取要删除规则的 ``id``。

   与 :mc:`mc ilm rule rm --all` 互斥。

全局标志
~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
--------

删除存储桶生命周期管理规则
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc:`mc ilm rule rm` 删除存储桶生命周期管理规则：

.. code-block:: shell
   :class: copyable

   mc ilm rule rm --id "RULE" ALIAS/PATH

- 将 ``RULE`` 替换为生命周期管理规则的唯一标识符。
  使用 :mc-cmd:`mc ilm rule ls` 查找要使用的 ID。

- 将 :mc-cmd:`ALIAS <mc ilm rule rm ALIAS>` 替换为 S3 兼容主机的 :mc:`alias <mc alias>`。

- 将 ``PATH`` 替换为 S3 兼容主机上存储桶的路径。

所需权限
--------------------

有关删除规则所需的权限，请参阅父命令中的 :ref:`required permissions <minio-mc-ilm-rule-permissions>`。


行为
--------

S3 兼容性
~~~~~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
