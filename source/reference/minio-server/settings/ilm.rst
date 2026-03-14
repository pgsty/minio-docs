.. _minio-server-envvar-ilm:

================
ILM 设置
================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

本页面介绍用于控制 MinIO 进程 ILM（信息生命周期管理）的设置。

.. include:: /includes/common-mc-admin-config.rst
   :start-after: start-minio-settings-defined
   :end-before: end-minio-settings-defined

.. include:: /includes/common-mc-admin-config.rst
   :start-after: start-minio-settings-test-before-prod
   :end-before: end-minio-settings-test-before-prod

过期 Worker
------------

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_ILM_EXPIRATION_WORKERS

   .. tab-item:: 配置项
      :sync: config
  
      .. mc-conf:: ilm expiration_workers
         :delimiter: " "

.. versionadded:: MinIO Server RELEASE.2024-03-03T17-50-39Z

设置用于 :ref:`对象过期 <minio-lifecycle-management-expiration>` 的 worker 数量。
有效值范围为 ``1`` 到 ``500``。

默认值为 ``100``。
