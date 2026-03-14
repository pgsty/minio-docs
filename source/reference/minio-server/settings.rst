.. _minio-environment-variables:
.. _minio-server-environment-variables:
.. _minio-server-configuration-settings:

=================
设置概览
=================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

:mc:`minio server` 进程将其配置存储在存储后端 :mc-cmd:`directory <minio server DIRECTORIES>` 中。

.. _minio-server-configuration-options:

MinIO 设置
--------------

MinIO 设置定义 MinIO :mc:`server <minio server>` 进程的运行时行为。

.. include:: /includes/common-mc-admin-config.rst
   :start-after: start-minio-settings-defined
   :end-before: end-minio-settings-defined

.. include:: /includes/common-mc-admin-config.rst
   :start-after: start-minio-settings-test-before-prod
   :end-before: end-minio-settings-test-before-prod

其他可用于自定义的设置包括：

- :ref:`核心设置 <minio-server-envvar-core>`
- :ref:`Root 凭证 <minio-server-envvar-root>`
- :ref:`存储类 <minio-server-envvar-storage-class>`
- :ref:`MinIO Console <minio-server-envvar-console>`
- :ref:`指标与日志 <minio-server-envvar-metrics-logging>`
- 用于 :ref:`MinIO 存储桶通知 <minio-bucket-notifications>` 的 :ref:`通知目标 <minio-server-envvar-notifications>`
- :ref:`身份与访问管理方案 <minio-server-envvar-iam>`
- :ref:`Key Encryption Service (KES) <minio-server-envvar-kes>`
- :ref:`Object Lambda 函数 <minio-server-envvar-object-lambda-webhook>`
