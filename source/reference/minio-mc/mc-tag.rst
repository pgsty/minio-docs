==========
``mc tag``
==========

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc tag


说明
----

.. start-mc-tag-desc

:mc:`mc tag` 命令用于添加、删除和列出与存储桶或对象关联的标签。

.. end-mc-tag-desc

MinIO 支持为对象最多添加 10 个自定义标签。

子命令
------

:mc:`mc tag` 包含以下子命令：

.. list-table::
   :header-rows: 1
   :widths: 30 70
   :width: 100%

   * - 子命令
     - 说明

   * - :mc:`~mc tag list`
     - .. include:: /reference/minio-mc/mc-tag-list.rst
          :start-after: start-mc-tag-list-desc
          :end-before: end-mc-tag-list-desc

   * - :mc:`~mc tag remove`
     - .. include:: /reference/minio-mc/mc-tag-remove.rst
          :start-after: start-mc-tag-remove-desc
          :end-before: end-mc-tag-remove-desc

   * - :mc:`~mc tag set`
     - .. include:: /reference/minio-mc/mc-tag-set.rst
          :start-after: start-mc-tag-set-desc
          :end-before: end-mc-tag-set-desc

.. toctree::
   :titlesonly:
   :hidden:
   
   /reference/minio-mc/mc-tag-set
   /reference/minio-mc/mc-tag-list
   /reference/minio-mc/mc-tag-remove
