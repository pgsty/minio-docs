============
``mc event``
============

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc event


描述
----

.. start-mc-event-desc

:mc:`mc event` 命令支持添加、删除和列出存储桶事件通知。

.. end-mc-event-desc

MinIO 会自动将触发的事件发送到已配置的通知目标。
MinIO 支持 AMQP (RabbitMQ)、Redis、ElasticSearch、NATS 和 PostgreSQL 等通知目标。
有关更多信息，请参阅 :ref:`MinIO 存储桶通知 <minio-bucket-notifications>`。


子命令
------

:mc:`mc event` 包含以下子命令：

.. list-table::
   :header-rows: 1
   :widths: 30 70
   :width: 100%

   * - 子命令
     - 描述

   * - :mc:`~mc event add`
     - .. include:: /reference/minio-mc/mc-event-add.rst
          :start-after: start-mc-event-add-desc
          :end-before: end-mc-event-add-desc

   * - :mc:`~mc event ls`
     - .. include:: /reference/minio-mc/mc-event-list.rst
          :start-after: start-mc-event-list-desc
          :end-before: end-mc-event-list-desc

   * - :mc:`~mc event rm`
     - .. include:: /reference/minio-mc/mc-event-remove.rst
          :start-after: start-mc-event-remove-desc
          :end-before: end-mc-event-remove-desc

.. toctree::
   :titlesonly:
   :hidden:
   
   /reference/minio-mc/mc-event-add
   /reference/minio-mc/mc-event-list
   /reference/minio-mc/mc-event-remove
