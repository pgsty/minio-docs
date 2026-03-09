================
``mc retention``
================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc retention


描述
----

.. start-mc-retention-desc

:mc:`mc retention` 命令用于为存储桶中的一个或多个对象配置 :ref:`Write-Once Read-Many (WORM) locking <minio-object-locking>` 设置。
你还可以为存储桶设置默认的对象锁设置；未显式配置对象锁设置的所有对象都会继承该存储桶默认值。

.. end-mc-retention-desc

子命令
------

:mc:`mc retention` 包含以下子命令：

.. list-table::
   :header-rows: 1
   :widths: 30 70
   :width: 100%

   * - 子命令
     - 描述

   * - :mc:`~mc retention clear`
     - .. include:: /reference/minio-mc/mc-retention-clear.rst
          :start-after: start-mc-retention-clear-desc
          :end-before: end-mc-retention-clear-desc

   * - :mc:`~mc retention info`
     - .. include:: /reference/minio-mc/mc-retention-info.rst
          :start-after: start-mc-retention-info-desc
          :end-before: end-mc-retention-info-desc

   * - :mc:`~mc retention set`
     - .. include:: /reference/minio-mc/mc-retention-set.rst
          :start-after: start-mc-retention-set-desc
          :end-before: end-mc-retention-set-desc

.. toctree::
   :titlesonly:
   :hidden:
   
   /reference/minio-mc/mc-retention-set
   /reference/minio-mc/mc-retention-info
   /reference/minio-mc/mc-retention-clear
