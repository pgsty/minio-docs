.. _minio-mc-ilm-rm:

=============
``mc ilm rm``
=============

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc ilm remove
.. mc:: mc ilm rm

.. versionchanged:: RELEASE.2022-12-24T15-21-38Z

   ``mc ilm rm`` 已被 :mc-cmd:`mc ilm rule rm` 取代。


语法
----

.. start-mc-ilm-rm-desc

:mc:`mc ilm rm` 命令用于从 MinIO 存储桶中移除一条对象生命周期管理规则。

.. end-mc-ilm-rm-desc

:mc:`mc ilm remove` 命令与 :mc:`mc ilm rm` 的功能等效。

.. tab-set::

   .. tab-item:: 示例

      以下命令从 ``myminio`` MinIO 部署的 ``mydata`` 存储桶中移除一条生命周期管理规则：

      .. code-block:: shell
         :class: copyable

         mc ilm rm --id "bgrt1ghju" myminio/mydata

   .. tab-item:: 语法

      该命令的语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] ilm rm                          \
                          --id "string" | (--all --force) \
                          ALIAS                           \

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~

.. mc-cmd:: ALIAS
   
   *Required* 要移除对象生命周期管理规则的 MinIO 部署中，
   :ref:`alias <alias>` 与存储桶完整路径。例如：

   .. code-block:: none

      mc ilm rm myminio/mydata

.. mc-cmd:: all

   *Required* 移除该存储桶中的所有规则。与
   :mc:`mc ilm rm id` 互斥。

   与 :mc:`mc ilm rm id` 互斥。

   需要同时包含 :mc-cmd:`~mc ilm rm force`。

.. mc-cmd:: force

   当指定 :mc-cmd:`~mc ilm rm all` 时必需。

.. mc-cmd:: id

   *Required* 规则的唯一 ID。使用 :mc:`mc ilm rule ls` 列出存储桶规则，
   并获取要移除规则的 ``id``。

   与 :mc:`mc ilm rm all` 互斥。

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

移除存储桶生命周期管理规则
~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc:`mc ilm rm` 移除一条存储桶生命周期管理规则：

.. code-block:: shell
   :class: copyable

   mc ilm rm --id "RULE" ALIAS/PATH

- 将 :mc-cmd:`RULE <mc ilm rm id>` 替换为生命周期管理规则的唯一名称。

- 将 :mc-cmd:`ALIAS <mc ilm rm ALIAS>` 替换为 S3 兼容主机的
  :mc:`alias <mc alias>`。

- 将 :mc-cmd:`PATH <mc ilm rm ALIAS>` 替换为 S3 兼容主机上的存储桶路径。

行为
----

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
