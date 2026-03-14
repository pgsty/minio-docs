==========
``mc ilm``
==========

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc ilm


描述
----

.. start-mc-ilm-desc

:mc:`mc ilm` 命令用于管理 MinIO 部署中的 :ref:`对象生命周期管理规则 <minio-lifecycle-management>` 和分层。

.. end-mc-ilm-desc

使用这些命令可以：

- 创建层
- 创建 :ref:`分层 <minio-lifecycle-management-tiering>` 规则
- 管理存储桶中对象的 :ref:`过期 <minio-lifecycle-management-expiration>` 规则
     

子命令
------

:mc:`mc ilm` 包含以下子命令：

.. list-table::
   :header-rows: 1
   :widths: 30 70
   :width: 100%

   * - 子命令
     - 描述

   * - :mc:`~mc ilm restore`
     - .. include:: /reference/minio-mc/mc-ilm-restore.rst
          :start-after: start-mc-ilm-restore-desc
          :end-before: end-mc-ilm-restore-desc

   * - :mc:`~mc ilm rule`
     - .. include:: /reference/minio-mc/mc-ilm-rule.rst
          :start-after: start-mc-ilm-rule-desc
          :end-before: end-mc-ilm-rule-desc

   * - :mc:`~mc ilm tier`
     - .. include:: /reference/minio-mc/mc-ilm-tier.rst
          :start-after: start-mc-ilm-tier-desc
          :end-before: end-mc-ilm-tier-desc

.. toctree::
   :titlesonly:
   :hidden:
   
   /reference/minio-mc/mc-ilm-restore
   /reference/minio-mc/mc-ilm-rule
   /reference/minio-mc/mc-ilm-tier
