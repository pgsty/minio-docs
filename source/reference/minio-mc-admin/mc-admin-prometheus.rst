=======================
``mc admin prometheus``
=======================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc admin prometheus

说明
----

.. start-mc-admin-prometheus-desc

:mc:`mc admin prometheus` 命令及其子命令用于访问 MinIO Prometheus 指标。

.. end-mc-admin-prometheus-desc

子命令
------

:mc:`mc admin prometheus` 包含以下子命令：

.. list-table::
   :header-rows: 1
   :widths: 30 70
   :width: 100%

   * - 子命令
     - 说明

   * - :mc:`~mc admin prometheus generate`
     - .. include:: /reference/minio-mc-admin/mc-admin-prometheus-generate.rst
          :start-after: start-mc-admin-prometheus-generate-desc
          :end-before: end-mc-admin-prometheus-generate-desc

   * - :mc:`~mc admin prometheus metrics`
     - .. include:: /reference/minio-mc-admin/mc-admin-prometheus-metrics.rst
          :start-after: start-mc-admin-prometheus-metrics-desc
          :end-before: end-mc-admin-prometheus-metrics-desc

.. toctree::
   :titlesonly:
   :hidden:
   
   /reference/minio-mc-admin/mc-admin-prometheus-generate
   /reference/minio-mc-admin/mc-admin-prometheus-metrics
