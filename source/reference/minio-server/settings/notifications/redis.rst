.. _minio-server-envvar-bucket-notification-redis:
.. _minio-server-config-bucket-notification-redis:

======================
Redis 通知设置
======================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

本页记录了将 Redis 服务配置为 :ref:`存储桶通知 <minio-bucket-notifications>` 目标所需的设置。
有关如何使用这些设置的教程，请参阅 :ref:`minio-bucket-notifications-publish-redis`。

.. include:: /includes/common-mc-admin-config.rst
   :start-after: start-minio-settings-defined
   :end-before: end-minio-settings-defined

.. include:: /includes/common-mc-admin-config.rst
   :start-after: start-minio-settings-test-before-prod
   :end-before: end-minio-settings-test-before-prod

多个 Redis 目标
----------------

你可以通过在每组相关 Redis 设置的顶层键末尾追加唯一标识符 ``_ID`` 来指定多个 Redis 服务端点。
例如，以下命令分别将两个不同的 Redis 服务端点设置为 ``PRIMARY`` 和 ``SECONDARY``：

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. code-block:: shell
         :class: copyable
      
         export MINIO_NOTIFY_REDIS_ENABLE_PRIMARY="on"
         export MINIO_NOTIFY_REDIS_ADDRESS_PRIMARY="redis-endpoint.example.net:9200"
         export MINIO_NOTIFY_REDIS_KEY_PRIMARY="bucketevents"
         export MINIO_NOTIFY_REDIS_FORMAT_PRIMARY="namespace"
      
      
         export MINIO_NOTIFY_REDIS_ENABLE_SECONDARY="on"
         export MINIO_NOTIFY_REDIS_REDIS_ADDRESS_SECONDARY="redis-endpoint2.example.net:9200"
         export MINIO_NOTIFY_REDIS_KEY_SECONDARY="bucketevents"
         export MINIO_NOTIFY_REDIS_FORMAT_SECONDARY="namespace"

   .. tab-item:: 配置设置
      :sync: config

      .. code-block:: shell

         mc admin config set notify_redis:primary              \ 
            address="redis-endpoint.example.net:9200"  \
            key="bucketevents"                                 \
            format="namespace"                                 \
            [ARGUMENT="VALUE"] ...                             \
   
         mc admin config set notify_redis:secondary            \
            address="redis-endpoint2.example.net:9200" \
            key="bucketevents"                                 \
            format="namespace"                                 \
            [ARGUMENT="VALUE"] ... 

设置
----

启用
~~~~

*必需*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_REDIS_ENABLE

      指定 ``on`` 以启用将存储桶通知发布到 Redis 服务端点。

      默认为 ``off``。
   
      如果设置为 ``on``，还必须指定以下附加环境变量：
   
      - :envvar:`MINIO_NOTIFY_REDIS_ADDRESS`
      - :envvar:`MINIO_NOTIFY_REDIS_KEY`
      - :envvar:`MINIO_NOTIFY_REDIS_FORMAT`
   
   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: notify_redis

      用于定义 Redis server/broker 端点并供 :ref:`MinIO bucket notifications <minio-bucket-notifications>` 使用的顶层配置键。
   
      使用 :mc-cmd:`mc admin config set` 设置或更新 Redis server/broker 端点。
      对于每个端点，以下参数为*必需*：
      
      - :mc-conf:`~notify_redis.address`
      - :mc-conf:`~notify_redis.key`
      - :mc-conf:`~notify_redis.format`
   
      将其他可选参数指定为以空白字符（``" "``）分隔的列表。
   
      .. code-block:: shell
         :class: copyable
   
         mc admin config set notify_redis \ 
            address="ENDPOINT" \
            key="<string>" \
            format="<string>" \
            [ARGUMENT="VALUE"] ... \

地址
~~~~

*必需*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_REDIS_ADDRESS

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: notify_redis address
         :delimiter: " "

指定 MinIO 发布存储桶事件的 Redis 服务端点。
例如：``redis.example.com:6369``。

.. include:: /includes/linux/minio-server.rst
   :start-after: start-notify-target-online-desc
   :end-before: end-notify-target-online-desc

键
~~

*必需*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_REDIS_KEY

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: notify_redis key
         :delimiter: " "

指定用于存储和更新事件的 Redis 键。
如果该键不存在，Redis 会自动创建。

格式
~~~~

*必需*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_REDIS_FORMAT

   .. tab-item:: 配置设置
      :sync: config
      
      .. mc-conf:: notify_redis format
         :delimiter: " "

指定写入 Redis 服务端点的事件数据格式。
MinIO 支持以下取值：

``namespace``
   对于每个存储桶事件，MinIO 会创建一个 JSON 文档，以事件中的存储桶名和对象名作为文档 ID，并将实际事件作为文档体的一部分。
   对该对象的后续更新会修改该对象现有的索引条目。
   同样，删除对象也会删除对应的索引条目。
   
``access``
   对于每个存储桶事件，MinIO 会创建一个包含事件详情的 JSON 文档，并以 Redis 生成的随机 ID 将其追加到该键。
   对对象的后续更新会产生新的索引条目，现有条目保持不变。

密码
~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_REDIS_PASSWORD

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: notify_redis password
         :delimiter: " "

指定 Redis 服务器的密码。

.. versionchanged:: RELEASE.2023-06-23T20-26-00Z

   当该值作为 :mc-cmd:`mc admin config get` 返回结果的一部分时，MinIO 会对其进行脱敏。

用户
~~~~

*可选*

.. versionadded:: RELEASE.2024-03-21T23-13-43Z

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_REDIS_USER

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: notify_redis user
         :delimiter: " "


指定 Redis 服务器的用户。

队列目录
~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_REDIS_QUEUE_DIR

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: notify_redis queue_dir
         :delimiter: " "

指定目录路径以启用 MinIO 的持久化事件存储，用于保存未投递消息，例如 ``/opt/minio/events``。

当 Redis server/broker 离线时，MinIO 会将未投递事件存储到指定存储中；连接恢复后，会重放这些已存储事件。

队列上限
~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_REDIS_QUEUE_LIMIT

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: notify_redis queue_limit
         :delimiter: " "

指定未投递消息的最大上限。
默认为 ``100000``。

注释
~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_REDIS_COMMENT

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: notify_redis comment
         :delimiter: " "

指定与 Redis 配置关联的注释。
