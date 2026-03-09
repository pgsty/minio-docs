.. _minio-mc-ilm-tier-check:

=====================
``mc ilm tier check``
=====================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc ilm tier check

描述
----

.. start-mc-ilm-tier-check-desc

:mc:`mc ilm tier check` 命令用于显示某个部署上远程层的配置。

.. end-mc-ilm-tier-check-desc

语法
----

该命令具有以下语法：

.. tab-set::

   .. tab-item:: EXAMPLE

      以下示例显示 ``myminio`` 部署上名为 ``WARM-TIER`` 的现有远程层配置。
      
      .. code-block:: shell
         :class: copyable

          mc ilm tier check myminio WARM-TIER                    


   .. tab-item:: SYNTAX
   
      该命令具有以下语法：

      .. code-block:: shell
         :class: copyable

         mc ilm tier add TARGET TIER_NAME 

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~

该命令接受以下参数：

.. mc-cmd:: TARGET
   :required:

   已配置 MinIO 部署的 :mc-cmd:`alias <mc alias>`，且目标层存在于该部署上。
      
.. mc-cmd:: TIER_NAME
   :required:

   要显示的现有远程层名称。

   **必须**使用全大写指定该层，例如 ``WARM_TIER``。
   

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals


示例
----

显示现有层的配置
~~~~~~~~~~~~~~~

以下示例显示 ``myminio`` 部署上 ``WARM-TIER`` 层的配置。

.. code-block:: shell
   :class: copyable

   mc ilm tier check myminio WARM-TIER 

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility

所需权限
--------

有关查看层所需的权限，请参阅父命令中的 :ref:`required permissions <minio-mc-ilm-tier-permissions>`。
