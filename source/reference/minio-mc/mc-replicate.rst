================
``mc replicate``
================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc replicate


说明
----

.. start-mc-replicate-desc

:mc:`mc replicate <mc replicate add>` 命令用于为 MinIO 部署配置和管理 :ref:`服务端存储桶复制 <minio-bucket-replication-serverside>`，包括 :ref:`双活复制配置 <minio-bucket-replication-serverside-twoway>` 和 :ref:`重新同步 <minio-replication-behavior-resync>`。

.. end-mc-replicate-desc

.. note::

   对于多站点复制，请参见 :mc:`mc admin replicate`。

子命令
------

:mc:`mc replicate` 包含以下子命令：

.. list-table::
   :header-rows: 1
   :widths: 30 70
   :width: 100%

   * - 子命令
     - 说明

   * - :mc:`~mc replicate add`
     - .. include:: /reference/minio-mc/mc-replicate-add.rst
          :start-after: start-mc-replicate-add-desc
          :end-before: end-mc-replicate-add-desc

   * - :mc:`~mc replicate backlog`
     - .. include:: /reference/minio-mc/mc-replicate-backlog.rst
          :start-after: start-mc-replicate-backlog-desc
          :end-before: end-mc-replicate-backlog-desc

   * - :mc:`~mc replicate export`
     - .. include:: /reference/minio-mc/mc-replicate-export.rst
          :start-after: start-mc-replicate-export-desc
          :end-before: end-mc-replicate-export-desc

   * - :mc:`~mc replicate import`
     - .. include:: /reference/minio-mc/mc-replicate-import.rst
          :start-after: start-mc-replicate-import-desc
          :end-before: end-mc-replicate-import-desc

   * - :mc:`~mc replicate ls`
     - .. include:: /reference/minio-mc/mc-replicate-ls.rst
          :start-after: start-mc-replicate-ls-desc
          :end-before: end-mc-replicate-ls-desc

   * - :mc:`~mc replicate resync`
     - .. include:: /reference/minio-mc/mc-replicate-resync.rst
          :start-after: start-mc-replicate-resync-desc
          :end-before: end-mc-replicate-resync-desc

   * - :mc:`~mc replicate rm`
     - .. include:: /reference/minio-mc/mc-replicate-rm.rst
          :start-after: start-mc-replicate-rm-desc
          :end-before: end-mc-replicate-rm-desc

   * - :mc:`~mc replicate status`
     - .. include:: /reference/minio-mc/mc-replicate-status.rst
          :start-after: start-mc-replicate-status-desc
          :end-before: end-mc-replicate-status-desc

   * - :mc:`~mc replicate update`
     - .. include:: /reference/minio-mc/mc-replicate-update.rst
          :start-after: start-mc-replicate-update-desc
          :end-before: end-mc-replicate-update-desc

.. toctree::
   :titlesonly:
   :hidden:
   
   /reference/minio-mc/mc-replicate-add
   /reference/minio-mc/mc-replicate-backlog
   /reference/minio-mc/mc-replicate-ls
   /reference/minio-mc/mc-replicate-update
   /reference/minio-mc/mc-replicate-resync
   /reference/minio-mc/mc-replicate-rm
   /reference/minio-mc/mc-replicate-status
   /reference/minio-mc/mc-replicate-export
   /reference/minio-mc/mc-replicate-import
