.. _minio-mc-admin-cluster-bucket:

===========================
``mc admin cluster bucket``
===========================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc admin cluster bucket

说明
----

.. versionadded:: RELEASE.2022-06-17T02-52-50Z

.. start-mc-admin-cluster-bucket-desc

:mc:`mc admin cluster bucket` 命令及其子命令提供了用于手动导入和导出 MinIO 存储桶元数据的工具。

.. end-mc-admin-cluster-bucket-desc

这些元数据包含与 :ref:`生命周期管理规则 <minio-lifecycle-management>` 等功能相关的配置。
你可以将这些元数据作为存储桶配置的快照，用于后续恢复，例如作为 :abbr:`BC/DR (Business Continuity / Disaster Recovery)` 或备份/恢复操作的一部分。

你可以将此命令用于单个存储桶，*或* 用于 MinIO 部署中的所有存储桶。
如需将部署中的所有存储桶自动同步到远端站点，请使用 :ref:`站点复制 <minio-site-replication-overview>`。

:mc:`mc admin cluster bucket` 命令包含以下子命令：

.. list-table::
   :header-rows: 1
   :widths: 40 60

   * - 子命令
     - 说明

   * - :mc:`~mc admin cluster bucket import`
     - .. include:: /reference/minio-mc-admin/mc-admin-cluster-bucket-import.rst
          :start-after: start-mc-admin-cluster-bucket-import-desc
          :end-before: end-mc-admin-cluster-bucket-import-desc

   * - :mc:`~mc admin cluster bucket export`
     - .. include:: /reference/minio-mc-admin/mc-admin-cluster-bucket-export.rst
          :start-after: start-mc-admin-cluster-bucket-export-desc
          :end-before: end-mc-admin-cluster-bucket-export-desc



.. toctree::
   :titlesonly:
   :hidden:

   /reference/minio-mc-admin/mc-admin-cluster-bucket-import
   /reference/minio-mc-admin/mc-admin-cluster-bucket-export

