============
``mc alias``
============

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc alias


说明
----

.. start-mc-alias-desc

:mc:`mc alias` 命令提供了一个便捷接口，用于管理 :mc-cmd:`mc` 可连接并执行操作的 S3 兼容主机列表。

.. end-mc-alias-desc

.. important:: 
   
   对 S3 兼容服务执行操作的 :mc-cmd:`mc` 命令 *必须* 为该服务指定一个别名。

子命令
------

:mc:`mc alias` 包含以下子命令：

.. list-table::
   :header-rows: 1
   :widths: 30 70
   :width: 100%

   * - 子命令
     - 说明

   * - :mc:`~mc alias list`
     - .. include:: /reference/minio-mc/mc-alias-list.rst
          :start-after: start-mc-alias-list-desc
          :end-before: end-mc-alias-list-desc

   * - :mc:`~mc alias remove`
     - .. include:: /reference/minio-mc/mc-alias-remove.rst
          :start-after: start-mc-alias-remove-desc
          :end-before: end-mc-alias-remove-desc

   * - :mc:`~mc alias set`
     - .. include:: /reference/minio-mc/mc-alias-set.rst
          :start-after: start-mc-alias-set-desc
          :end-before: end-mc-alias-set-desc

   * - :mc:`~mc alias import`
     - .. include:: /reference/minio-mc/mc-alias-import.rst
          :start-after: start-mc-alias-import-desc
          :end-before: end-mc-alias-import-desc

   * - :mc:`~mc alias export`
     - .. include:: /reference/minio-mc/mc-alias-export.rst
          :start-after: start-mc-alias-export-desc
          :end-before: end-mc-alias-export-desc

.. toctree::
   :titlesonly:
   :hidden:
   
   /reference/minio-mc/mc-alias-list
   /reference/minio-mc/mc-alias-remove
   /reference/minio-mc/mc-alias-set
   /reference/minio-mc/mc-alias-import
   /reference/minio-mc/mc-alias-export
