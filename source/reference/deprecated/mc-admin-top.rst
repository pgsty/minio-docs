================
``mc admin top``
================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc admin top

.. versionchanged:: RELEASE.2022-08-11T00-30-48Z

   ``mc admin top`` 已由 :mc-cmd:`mc support top` 替代。

描述
----

.. start-mc-admin-top-desc

:mc-cmd:`mc admin top` 命令返回分布式 MinIO 部署的统计信息，
类似于 ``top`` 命令的输出。

.. end-mc-admin-top-desc

.. admonition:: 仅在 MinIO 部署上使用 ``mc admin``
   :class: note

   .. include:: /includes/facts-mc-admin.rst
      :start-after: start-minio-only
      :end-before: end-minio-only

语法
----

.. mc-cmd:: locks
   :fullpath:

   返回 MinIO 部署中最早的 10 个锁。

   该命令的语法如下：

   .. code-block:: shell
      :class: copyable

      mc admin top locks TARGET

   该命令支持以下参数：

   .. mc-cmd:: TARGET

      已配置 MinIO 部署的 :mc-cmd:`alias <mc alias>`，命令将从该部署
      获取统计信息。

      该 alias *必须* 对应分布式（多节点）MinIO 部署。
      对于 :term:`single-node single-drive` 部署，该命令会返回错误。
