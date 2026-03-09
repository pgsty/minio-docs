.. _minio-server-envvar-bucket-notification-mqtt:
.. _minio-server-config-bucket-notification-mqtt:

==========================
MQTT 通知设置
==========================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

本页面记录了将 MQTT 服务配置为 :ref:`存储桶通知 <minio-bucket-notifications>` 目标的相关设置。
有关如何使用这些设置的教程，请参阅 :ref:`minio-bucket-notifications-publish-mqtt`。

.. include:: /includes/common-mc-admin-config.rst
   :start-after: start-minio-settings-defined
   :end-before: end-minio-settings-defined

.. include:: /includes/common-mc-admin-config.rst
   :start-after: start-minio-settings-test-before-prod
   :end-before: end-minio-settings-test-before-prod

多个 MQTT 目标
--------------

你可以在顶层键后为每组相关 MQTT 设置追加一个唯一标识符 ``_ID``，以指定多个 MQTT 服务端点。
例如，以下命令分别将两个不同的 MQTT 服务端点设置为 ``PRIMARY`` 和 ``SECONDARY``：

.. tab-set:: 
   
   .. tab-item:: 环境变量
      :sync: envvar

      .. code-block:: shell
         :class: copyable
   
         export MINIO_NOTIFY_MQTT_ENABLE_PRIMARY="on"
         export MINIO_NOTIFY_MQTT_BROKER_PRIMARY="tcp://user:password@mqtt-endpoint.example.net:1883"
   
         export MINIO_NOTIFY_MQTT_ENABLE_SECONDARY="on"
         export MINIO_NOTIFY_MQTT_BROKER_SECONDARY="tcp://user:password@mqtt-endpoint.example.net:1883"

   .. tab-item:: 配置项
      :sync: config

      .. code-block:: shell

         mc admin config set notify_mqtt:primary \ 
            broker="tcp://endpoint:port" \
            topic="minio/bucket-name/events/" \
            username="username" \
            password="password" \
            [ARGUMENT="VALUE"] ... \
   
         mc admin config set notify_mqtt:secondary \
            broker="tcp://endpoint:port" \
            topic="minio/bucket-name/events/" \
            username="username" \
            password="password" \
            [ARGUMENT="VALUE"] ... \

在这些设置中，:envvar:`MINIO_NOTIFY_MQTT_ENABLE_PRIMARY <MINIO_NOTIFY_MQTT_ENABLE>` 表示该环境变量与 ID 为 ``PRIMARY`` 的 MQTT 服务端点关联。

设置
----

启用
~~~~

*必填*

.. tab-set:: 
   
   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_MQTT_ENABLE

      指定 ``on`` 以启用将存储桶通知发布到 MQTT 端点。
      
      默认为 ``off``。

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: notify_mqtt

      用于定义 MQTT server/broker 端点的顶层配置键，可用于 :ref:`MinIO 存储桶通知 <minio-bucket-notifications>`。
   
      使用 :mc-cmd:`mc admin config set` 设置或更新 MQTT server/broker 端点。
      每个端点都*必须*包含以下参数：
      
      - :mc-conf:`~notify_mqtt.broker`
      - :mc-conf:`~notify_mqtt.topic`
      - :mc-conf:`~notify_mqtt.username` *如果 MQTT server/broker 不强制认证/授权，则为可选*
      - :mc-conf:`~notify_mqtt.password` *如果 MQTT server/broker 不强制认证/授权，则为可选*

      其他可选参数请以空白字符（``" "``）分隔的列表形式指定。

      .. code-block:: shell
         :class: copyable
   
         mc admin config set notify_mqtt \ 
            broker="tcp://endpoint:port" \
            topic="minio/bucket-name/events/" \
            username="username" \
            password="password" \
            [ARGUMENT="VALUE"] ... \

Broker
~~~~~~

*必填*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_MQTT_BROKER

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: notify_mqtt broker
         :delimiter: " "

指定 MQTT server/broker 端点。
MinIO 支持通过 TCP、TLS 或 Websocket 连接到 server/broker URL。
例如：

- ``tcp://mqtt.example.net:1883``
- ``tls://mqtt.example.net:1883``
- ``ws://mqtt.example.net:1883``

.. include:: /includes/linux/minio-server.rst
   :start-after: start-notify-target-online-desc
   :end-before: end-notify-target-online-desc

Topic
~~~~~

*必填*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_MQTT_TOPIC

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: notify_mqtt topic
         :delimiter: " "

指定 MQTT topic 名称，用于关联 MinIO 发布到 MQTT 端点的事件。

Username
~~~~~~~~

*如果 MQTT server/broker 强制认证/授权，则必填*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_MQTT_USERNAME

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: notify_mqtt username
         :delimiter: " "

指定 MinIO 用于向 MQTT server/broker 进行身份认证的 MQTT 用户名。

Password
~~~~~~~~

*如果 MQTT server/broker 强制认证/授权，则必填*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_MQTT_PASSWORD

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: notify_mqtt password
         :delimiter: " "

指定 MinIO 用于向 MQTT server/broker 进行身份认证的 MQTT 用户名对应的密码。

.. versionchanged:: RELEASE.2023-06-23T20-26-00Z

   作为 :mc-cmd:`mc admin config get` 返回结果的一部分时，MinIO 会对该值进行脱敏处理。

服务质量（QoS）
~~~~~~~~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_MQTT_QOS

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: notify_mqtt qos
         :delimiter: " "

指定已发布事件的 Quality of Service 优先级。

默认为 ``0``。

Keep Alive 间隔
~~~~~~~~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_MQTT_KEEP_ALIVE_INTERVAL

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: notify_mqtt keep_alive_interval
         :delimiter: " "

指定 MQTT 连接的 keep-alive 间隔。MinIO
支持以下时间单位：

- ``s`` - 秒，例如 "60s"
- ``m`` - 分钟，例如 "60m"
- ``h`` - 小时，例如 "24h"
- ``d`` - 天，例如 "7d"

重连间隔
~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_MQTT_RECONNECT_INTERVAL

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: notify_mqtt reconnect_interval
         :delimiter: " "

指定 MQTT 连接的重连间隔。MinIO
支持以下时间单位：

- ``s`` - 秒，例如 "60s"
- ``m`` - 分钟，例如 "60m"
- ``h`` - 小时，例如 "24h"
- ``d`` - 天，例如 "7d"

队列目录
~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_MQTT_QUEUE_DIR

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: notify_mqtt queue_dir 
         :delimiter: " "

指定目录路径以启用 MinIO 的持久化事件存储，用于保存未投递消息，例如 ``/opt/minio/events``。

当 MQTT server/broker 离线时，MinIO 会将未投递事件存储在指定位置；连接恢复后会重放这些已存储事件。

队列上限
~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_MQTT_QUEUE_LIMIT

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: notify_mqtt queue_limit 
         :delimiter: " "

指定未投递消息的最大上限。
默认为 ``100000``。

注释
~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_MQTT_COMMENT

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: notify_mqtt comment 
         :delimiter: " "

指定与 MQTT 配置关联的注释。
