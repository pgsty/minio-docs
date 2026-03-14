.. _minio-mc-admin-cluster-iam:

========================
``mc admin cluster iam``
========================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc admin cluster iam

描述
----

.. versionadded:: RELEASE.2022-06-26T18-51-48Z

.. start-mc-admin-cluster-iam-desc

:mc:`mc admin cluster iam` 命令及其子命令提供了用于手动导入和导出 MinIO :ref:`身份与访问管理（IAM） <minio-authentication-and-identity-management>` 元数据的工具。

.. end-mc-admin-cluster-iam-desc

如需将部署中的所有 IAM 配置自动同步到远程站点，请使用 :ref:`站点复制 <minio-site-replication-overview>`。

:mc:`mc admin cluster iam` 命令包含以下子命令：

.. list-table::
   :header-rows: 1
   :widths: 40 60

   * - 子命令
     - 描述

   * - :mc:`~mc admin cluster iam import`
     - .. include:: /reference/minio-mc-admin/mc-admin-cluster-iam-import.rst
          :start-after: start-mc-admin-cluster-iam-import-desc
          :end-before: end-mc-admin-cluster-iam-import-desc

   * - :mc:`~mc admin cluster iam export`
     - .. include:: /reference/minio-mc-admin/mc-admin-cluster-iam-export.rst
          :start-after: start-mc-admin-cluster-iam-export-desc
          :end-before: end-mc-admin-cluster-iam-export-desc



.. toctree::
   :titlesonly:
   :hidden:

   /reference/minio-mc-admin/mc-admin-cluster-iam-import
   /reference/minio-mc-admin/mc-admin-cluster-iam-export

