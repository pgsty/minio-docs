.. _minio-mc-ilm-tier-ls:

==================
``mc ilm tier ls``
==================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc ilm tier list
.. mc:: mc ilm tier ls

.. versionchanged:: RELEASE.2022-12-24T15-21-38Z

   :mc:`mc ilm tier ls` replaces ``mc admin tier ls``.

描述
----

.. start-mc-ilm-tier-ls-desc

:mc:`mc ilm tier ls` 命令显示某个部署上已配置的远程层级。

.. end-mc-ilm-tier-ls-desc

:mc:`mc ilm tier list` 命令与 :mc:`mc ilm tier ls` 功能等效。

语法
----

该命令的语法如下：

.. tab-set::

   .. tab-item:: 示例

      以下示例输出 ``myminio`` 部署上现有远程层级的列表。
      
      .. code-block:: shell
         :class: copyable

          mc ilm tier ls myminio


   .. tab-item:: 语法
   
      该命令的语法如下：

      .. code-block:: shell
         :class: copyable

         mc ilm tier ls TARGET TIER_NAME 

参数
~~~~

该命令接受以下参数：

.. mc-cmd:: TARGET
   :required:

   已配置 MinIO 部署的 :mc-cmd:`alias <mc alias>`，且目标层级存在于该部署上。


全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility

所需权限
--------

有关查看层级所需的权限，请参阅父命令中的 :ref:`required permissions <minio-mc-ilm-tier-permissions>`。
