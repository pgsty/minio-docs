======================
监控存储桶与对象事件
======================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 1

存储桶通知
----------

MinIO 存储桶通知允许管理员在特定对象或存储桶事件发生时，将通知发送到受支持的外部服务。
MinIO 支持与 :s3-docs:`Amazon S3 Event Notifications <NotificationHowTo.html>`
类似的存储桶级和对象级 S3 事件。

在某些受支持的事件上，MinIO 支持将存储桶或对象事件发布到以下受支持的目标。

- :ref:`minio-bucket-notifications-publish-amqp`
- :ref:`minio-bucket-notifications-publish-mqtt`
- :ref:`minio-bucket-notifications-publish-nats`
- :ref:`minio-bucket-notifications-publish-nsq`
- :ref:`minio-bucket-notifications-publish-elasticsearch`
- :ref:`minio-bucket-notifications-publish-kafka`
- :ref:`minio-bucket-notifications-publish-mysql`
- :ref:`minio-bucket-notifications-publish-postgresql`
- :ref:`minio-bucket-notifications-publish-redis`
- :ref:`minio-bucket-notifications-publish-webhook` 

有关 MinIO 存储桶通知的更完整文档，请参见 :ref:`minio-bucket-notifications`。

部署指标
--------

MinIO 提供兼容 Prometheus 的端点，以支持对指标进行时间序列查询。

服务日志
--------

MinIO 提供以下接口用于远程读取服务日志：

- :mc:`mc admin logs` 命令会返回指定服务器的控制台输出。
- MinIO 支持将服务日志推送到 HTTP webhook，以便进一步采集。
  更多信息请参见 :ref:`minio-logging-publish-server-logs`。

.. toctree::
   :titlesonly:
   :hidden:

   /administration/monitoring/bucket-notifications
