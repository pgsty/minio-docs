.. _minio-bucket-notifications:

================
存储桶通知
================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

MinIO 存储桶通知允许管理员在特定对象或存储桶事件发生时，将通知发送到受支持的外部服务。
MinIO 支持与 :s3-docs:`Amazon S3 Event Notifications <NotificationHowTo.html>`
类似的存储桶级和对象级 S3 事件。

支持的通知目标
--------------

MinIO 支持将事件通知发布到以下目标：

.. list-table::
   :header-rows: 1
   :widths: 30 70
   :width: 100%

   * - 目标
     - 说明

   * - :guilabel:`AMQP` (RabbitMQ)
     - 将通知发布到 AMQP 服务，例如
       `RabbitMQ <https://www.rabbitmq.com>`__.

       教程参见 :ref:`minio-bucket-notifications-publish-amqp`。

   * - :guilabel:`MQTT`
     - 将通知发布到 `MQTT <https://www.mqtt.org/>`__ 服务。

       教程参见 :ref:`minio-bucket-notifications-publish-mqtt`。

   * - :guilabel:`NATS`
     - 将通知发布到 `NATS <https://nats.io/>`__ 服务。

       教程参见 :ref:`minio-bucket-notifications-publish-nats`。

   * - :guilabel:`NSQ`
     - 将通知发布到 `NSQ <https://nsq.io/>`__ 服务。

       教程参见 :ref:`minio-bucket-notifications-publish-nsq`

   * - :guilabel:`Elasticsearch`
     - 将通知发布到 `Elasticsearch <https://www.elastic.co/>`__ 服务。

       教程参见 :ref:`minio-bucket-notifications-publish-elasticsearch`。

   * - :guilabel:`Kafka`
     - 将通知发布到 `Kafka <https://kafka.apache.org/>`__ 服务。

       教程参见 :ref:`minio-bucket-notifications-publish-kafka`。

   * - :guilabel:`MySQL`
     - 将通知发布到 `MySQL <https://www.mysql.com/>`__ 服务。

       教程参见 :ref:`minio-bucket-notifications-publish-mysql`。

   * - :guilabel:`PostgreSQL`
     - 将通知发布到 `PostgreSQL <https://www.postgresql.org/>`__ 服务。

       教程参见 :ref:`minio-bucket-notifications-publish-postgresql`。

   * - :guilabel:`Redis`
     - 将通知发布到 `Redis <https://redis.io/>`__ 服务。

       教程参见 :ref:`minio-bucket-notifications-publish-redis`。

   * - :guilabel:`webhook`
     - 将通知发布到 `Webhook
       <https://en.wikipedia.org/wiki/Webhook>`__ 服务。

       教程参见 :ref:`minio-bucket-notifications-publish-webhook`。

异步与同步存储桶通知
--------------------

.. versionadded:: RELEASE.2023-06-23T20-26-00Z

   对于 *所有* 远程目标，MinIO 支持异步（默认）或同步存储桶通知。

使用异步传递时，MinIO 会将事件发送到已配置的远程目标，并且在继续处理下一个事件之前 *不会* 等待响应。
异步存储桶通知优先保证发送速率，但如果远程目标在传输或处理期间出现瞬时问题，则存在部分事件丢失的风险。

使用同步传递时，MinIO 会将事件发送到已配置的远程目标，然后等待远程目标确认已成功接收后，才继续处理下一个事件。
同步存储桶通知优先保证事件可送达，但代价是事件发送速率较慢且队列更容易被填满。

要为 *所有已配置的远程目标* 启用同步存储桶通知，请使用以下任一设置：

- 将 :envvar:`MINIO_API_SYNC_EVENTS` 环境变量设置为 ``on``，然后重启 MinIO 部署。

- 将 :mc-conf:`api.sync_events` 配置项设置为 ``on``，然后重启 MinIO 部署。

.. note::

   对于同步和异步事件，MinIO 都会为每个远程目标维护一个队列，用于存储尚未发送和待处理的事件。
   队列上限默认为 ``100000``。

   队列已满时，MinIO 会丢弃新事件。

   可按需增大队列大小，以更好地适配 MinIO 部署与远程目标的事件发送和处理速率。
   使用对应通知方法的 ``QUEUE_LIMIT`` 环境变量或配置项来修改该限制。

   对于异步事件，MinIO 最多允许 ``50000`` 个并发 ``send`` 调用。

.. _minio-bucket-notifications-event-types:

支持的 S3 事件类型
------------------

MinIO 存储桶通知与 :s3-docs:`Amazon S3 Event Notifications <NotificationHowTo.html>` 兼容。
本节列出所有受支持的事件。

对象事件
~~~~~~~~

MinIO 支持在以下 S3 对象事件上触发通知：

.. data:: s3:ObjectAccessed:Get
.. data:: s3:ObjectAccessed:GetLegalHold
.. data:: s3:ObjectAccessed:GetRetention
.. data:: s3:ObjectAccessed:Head
.. data:: s3:ObjectCreated:CompleteMultipartUpload
.. data:: s3:ObjectCreated:Copy
.. data:: s3:ObjectCreated:DeleteTagging
.. data:: s3:ObjectCreated:Post
.. data:: s3:ObjectCreated:Put
.. data:: s3:ObjectCreated:PutLegalHold
.. data:: s3:ObjectCreated:PutRetention
.. data:: s3:ObjectCreated:PutTagging
.. data:: s3:ObjectRemoved:Delete
.. data:: s3:ObjectRemoved:DeleteMarkerCreated

指定通配符 ``*`` 可选择与某个前缀相关的所有事件：

.. data:: s3:ObjectAccessed:*

   选择所有以 ``s3:ObjectAccessed`` 为前缀的事件。
   
.. data:: s3:ObjectCreated:*

   选择所有以 ``s3:ObjectCreated`` 为前缀的事件。

.. data:: s3:ObjectRemoved:*

   选择所有以 ``s3:ObjectRemoved`` 为前缀的事件。

复制事件
~~~~~~~~

MinIO 支持在以下 S3 复制事件上触发通知：

.. data:: s3:Replication:OperationCompletedReplication
.. data:: s3:Replication:OperationFailedReplication
.. data:: s3:Replication:OperationMissedThreshold
.. data:: s3:Replication:OperationNotTracked
.. data:: s3:Replication:OperationReplicatedAfterThreshold

指定通配符 ``*`` 可选择所有 ``s3:Replication`` 事件：

.. data:: s3:Replication:*

ILM 转换事件
~~~~~~~~~~~~

MinIO 支持在以下 S3 ILM 转换事件上触发通知：

.. data:: s3:ObjectRestore:Post
.. data:: s3:ObjectRestore:Completed
.. data:: s3:ObjectTransition:Failed
.. data:: s3:ObjectTransition:Complete

指定通配符 ``*`` 可选择与某个前缀相关的所有事件：

.. data:: s3:ObjectTransition:*

   选择所有以 ``s3:ObjectTransition`` 为前缀的事件。

.. data:: s3:ObjectRestore:*

   选择所有以 ``s3:ObjectRestore`` 为前缀的事件。

Scanner 事件
~~~~~~~~~~~~

MinIO 支持在以下 S3 :ref:`scanner <minio-concepts-scanner>` 转换事件上触发通知：

.. data:: s3:Scanner:ManyVersions

   :ref:`Scanner <minio-concepts-scanner>` 发现具有超过 1,000 个版本的对象。

.. data:: s3:Scanner:BigPrefix

   :ref:`Scanner <minio-concepts-scanner>` 发现具有超过 50,000 个子文件夹的前缀。

全局事件
~~~~~~~~

MinIO 支持在以下全局事件上触发通知。
只能通过 `ListenNotification <https://silo.pigsty.cc/developers/go/API.html#listennotification-context-context-context-prefix-suffix-string-events-string-chan-notification-info>`__ API 监听这些事件：

.. data:: s3:BucketCreated
.. data:: s3:BucketRemoved

.. todo


负载模式
--------

所有通知负载都使用相同的整体模式。
根据通知类型的不同，某些字段可能会省略或为 null。

.. code-block:: json

   {
       "eventVersion": "string",
       "eventSource": "string",
       "awsRegion": "string",
       "eventTime": "string",
       "eventName": "string",
       "userIdentity": {
           "principalId": "string"
       },
       "requestParameters": {
           "key": "value"
       },
       "responseElements": {
           "key": "value"
       },
       "s3": {
           "s3SchemaVersion": "string",
           "configurationId": "string",
           "bucket": {
               "name": "string",
               "ownerIdentity": {
                   "principalId": "string"
               },
               "arn": "string"
           },
           "object": {
               "key": "string",
               "size": 10000,
               "eTag": "string",
               "contentType": "string",
               "userMetadata": {
                   "key": "string"
               },
               "versionId": "string",
               "sequencer": "string"
           }
       },
       "source": {
           "host": "string",
           "port": "string",
           "userAgent": "string"
       }
   }


示例
~~~~

以下示例是 ``s3:ObjectCreated:Put`` 事件的通知：

.. code-block:: json

   {
     "EventName": "s3:ObjectCreated:Put",
     "Key": "test-bucket/image.jpg",
     "Records": [
       {
         "eventVersion": "2.0",
         "eventSource": "minio:s3",
         "awsRegion": "",
         "eventTime": "2025-02-06T01:04:31.998Z",
         "eventName": "s3:ObjectCreated:Put",
         "userIdentity": {
           "principalId": "access_key"
         },
         "requestParameters": {
           "principalId": "access_key",
           "region": "",
           "sourceIPAddress": "192.168.1.10"
         },
         "responseElements": {
           "x-amz-id-2": "dd9025bab4ad464b049177c95eb6ebf374d3b3fd1af9251148b658df7ac2e3e8",
           "x-amz-request-id": "182178E8B36AC9DF",
           "x-minio-deployment-id": "2369dcb4-348b-4d30-8fc9-61ab089ba4bc",
           "x-minio-origin-endpoint": "https://minio.test.svc.cluster.local"
         },
         "s3": {
           "s3SchemaVersion": "1.0",
           "configurationId": "Config",
           "bucket": {
             "name": "test-bucket",
             "ownerIdentity": {
               "principalId": "access_key"
             },
             "arn": "arn:aws:s3:::test-bucket"
           },
           "object": {
             "key": "image.jpg",
             "size": 84452,
             "eTag": "eb52f8e46f60a27a8a1a704e25757f30",
             "contentType": "image/jpeg",
             "userMetadata": {
               "content-type": "image/jpeg"
             },
             "sequencer": "182178E8B3728CAC"
           }
         },
         "source": {
           "host": "192.168.1.10",
           "port": "",
           "userAgent": "MinIO (linux; amd64) minio-go/v7.0.83"
         }
       }
     ]
   }


.. toctree::
   :titlesonly:
   :hidden:

   /administration/monitoring/publish-events-to-amqp
   /administration/monitoring/publish-events-to-mqtt
   /administration/monitoring/publish-events-to-nats
   /administration/monitoring/publish-events-to-nsq
   /administration/monitoring/publish-events-to-elasticsearch
   /administration/monitoring/publish-events-to-kafka
   /administration/monitoring/publish-events-to-mysql
   /administration/monitoring/publish-events-to-postgresql
   /administration/monitoring/publish-events-to-redis
   /administration/monitoring/publish-events-to-webhook
