============
``mc batch``
============

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc batch


.. versionadded:: mc RELEASE.2023-03-20T17-17-53Z

   新增了通过 :mc:`mc batch cancel` 命令取消作业的能力。


描述
----

.. start-mc-batch-desc

:mc:`mc batch` 命令允许您在 MinIO 部署上运行一个或多个作业任务。

.. end-mc-batch-desc

子命令
------

:mc-cmd:`mc batch` 包含以下子命令：

.. list-table::
   :header-rows: 1
   :widths: 30 70
   :width: 100%

   * - 子命令
     - 描述

   * - :mc:`~mc batch cancel`
     - .. include:: /reference/minio-mc/mc-batch-cancel.rst
          :start-after: start-mc-batch-cancel-desc
          :end-before: end-mc-batch-cancel-desc

   * - :mc:`~mc batch describe`
     - .. include:: /reference/minio-mc/mc-batch-describe.rst
          :start-after: start-mc-batch-describe-desc
          :end-before: end-mc-batch-describe-desc
   
   * - :mc:`~mc batch generate`
     - .. include:: /reference/minio-mc/mc-batch-generate.rst
          :start-after: start-mc-batch-generate-desc
          :end-before: end-mc-batch-generate-desc

   * - :mc:`~mc batch list`
     - .. include:: /reference/minio-mc/mc-batch-list.rst
          :start-after: start-mc-batch-list-desc
          :end-before: end-mc-batch-list-desc

   * - :mc:`~mc batch start`
     - .. include:: /reference/minio-mc/mc-batch-start.rst
          :start-after: start-mc-batch-start-desc
          :end-before: end-mc-batch-start-desc

   * - :mc:`~mc batch status`
     - .. include:: /reference/minio-mc/mc-batch-status.rst
          :start-after: start-mc-batch-status-desc
          :end-before: end-mc-batch-status-desc

.. toctree::
   :titlesonly:
   :hidden:
   
   /reference/minio-mc/mc-batch-cancel
   /reference/minio-mc/mc-batch-describe
   /reference/minio-mc/mc-batch-generate
   /reference/minio-mc/mc-batch-list
   /reference/minio-mc/mc-batch-start
   /reference/minio-mc/mc-batch-status
