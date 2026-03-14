====================
``mc admin scanner``
====================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc admin scanner


说明
----

.. start-mc-admin-scanner-desc

:mc:`mc admin scanner` 命令提供有关 :ref:`scanner <minio-concepts-scanner>` 进程的信息。

.. end-mc-admin-scanner-desc

子命令
------

:mc:`mc admin scanner` 包含以下子命令：

.. list-table::
   :header-rows: 1
   :widths: 30 70
   :width: 100%

   * - 子命令
     - 说明

   * - :mc:`~mc admin scanner status`
     - .. include:: /reference/minio-mc-admin/mc-admin-scanner-status.rst
          :start-after: start-mc-admin-scanner-status-desc
          :end-before: end-mc-admin-scanner-status-desc

   * - :mc:`~mc admin scanner trace`
     - .. include:: /reference/minio-mc-admin/mc-admin-scanner-trace.rst
          :start-after: start-mc-admin-scanner-trace-desc
          :end-before: end-mc-admin-scanner-trace-desc

.. toctree::
   :titlesonly:
   :hidden:
   
   /reference/minio-mc-admin/mc-admin-scanner-status
   /reference/minio-mc-admin/mc-admin-scanner-trace
