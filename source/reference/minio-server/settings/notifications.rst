.. _minio-server-envvar-notifications:
.. _minio-server-config-logging-logs:

=====================
存储桶通知设置
=====================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

本页介绍用于控制 :ref:`MinIO bucket notifications <minio-bucket-notifications>` 相关行为的设置。

.. include:: /includes/common-mc-admin-config.rst
   :start-after: start-minio-settings-defined
   :end-before: end-minio-settings-defined

.. include:: /includes/common-mc-admin-config.rst
   :start-after: start-minio-settings-test-before-prod
   :end-before: end-minio-settings-test-before-prod

同步事件
--------

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_API_SYNC_EVENTS

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: api sync_events         

.. include:: /includes/common-mc-admin-config.rst
   :start-after: start-minio-api-sync-events
   :end-before: end-minio-api-sync-events

支持的通知目标
--------------

通知需要一个用于接收事件的目标。
MinIO 支持多种通知目标。
每种目标类型的设置位于各自独立的页面。
请根据实际使用的目标类型，选择下方对应链接。

- :ref:`minio-server-envvar-bucket-notification-amqp`
- :ref:`minio-server-envvar-bucket-notification-elasticsearch`
- :ref:`minio-server-envvar-bucket-notification-kafka`
- :ref:`minio-server-envvar-bucket-notification-mqtt`
- :ref:`minio-server-envvar-bucket-notification-mysql`
- :ref:`minio-server-envvar-bucket-notification-nats`
- :ref:`minio-server-envvar-bucket-notification-nsq`
- :ref:`minio-server-envvar-bucket-notification-postgresql`
- :ref:`minio-server-envvar-bucket-notification-redis`
- :ref:`minio-server-envvar-bucket-notification-webhook`

.. toctree::
   :titlesonly:
   :hidden:
   
   /reference/minio-server/settings/notifications/amqp
   /reference/minio-server/settings/notifications/elasticsearch
   /reference/minio-server/settings/notifications/kafka
   /reference/minio-server/settings/notifications/mqtt
   /reference/minio-server/settings/notifications/mysql
   /reference/minio-server/settings/notifications/nats
   /reference/minio-server/settings/notifications/nsq
   /reference/minio-server/settings/notifications/postgresql
   /reference/minio-server/settings/notifications/redis
   /reference/minio-server/settings/notifications/webhook-service
   
