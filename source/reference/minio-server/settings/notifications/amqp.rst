.. _minio-server-envvar-bucket-notification-amqp:
.. _minio-server-config-bucket-notification-amqp:

==================
AMQP 通知设置
==================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

本文档说明了将 AMQP 服务配置为 :ref:`存储桶通知 <minio-bucket-notifications>` 目标的相关设置。
有关如何使用这些设置的教程，请参阅 :ref:`minio-bucket-notifications-publish-amqp`。

.. include:: /includes/common-mc-admin-config.rst
   :start-after: start-minio-settings-defined
   :end-before: end-minio-settings-defined

.. include:: /includes/common-mc-admin-config.rst
   :start-after: start-minio-settings-test-before-prod
   :end-before: end-minio-settings-test-before-prod

多个 AMQP 目标
----------------

可以通过在顶层键后为每组相关 AMQP 设置追加唯一标识符 ``_ID``，来指定多个 AMQP 服务端点。

示例
~~~~

例如，以下命令分别将两个不同的 AMQP 服务端点设置为 ``PRIMARY`` 和 ``SECONDARY``：

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. code-block:: shell
         :class: copyable

         export MINIO_NOTIFY_AMQP_ENABLE_PRIMARY="on"
         export MINIO_NOTIFY_AMQP_URL_PRIMARY="amqp://user:password@amqp-endpoint.example.net:5672"

         export MINIO_NOTIFY_AMQP_ENABLE_SECONDARY="on"
         export MINIO_NOTIFY_AMQP_URL_SECONDARY="amqp://user:password@amqp-endpoint.example.net:5672"

      例如，:envvar:`MINIO_NOTIFY_AMQP_ENABLE_PRIMARY <MINIO_NOTIFY_AMQP_ENABLE>` 表示该环境变量关联到 ID 为 ``PRIMARY`` 的 AMQP 服务端点。

   .. tab-item:: 配置设置
      :sync: config

      .. code-block:: shell

         mc admin config set notify_amqp:primary \ 
            url="user:password@amqp://amqp-endpoint.example.net:5672" [ARGUMENT=VALUE ...]

         mc admin config set notify_amqp:secondary \
            url="user:password@amqp://amqp-endpoint.example.net:5672" [ARGUMENT=VALUE ...]

      请注意，对于配置设置，唯一标识符仅追加到 ``amqp``，而不是每个单独参数。


设置
----

启用
~~~~

.. tab-set::

   .. tab-item:: 环境变量
      :selected:

      .. envvar:: MINIO_NOTIFY_AMQP_ENABLE

      如果设置为 ``on``，则必须指定 :envvar:`MINIO_NOTIFY_AMQP_URL`。

      指定 ``on`` 以启用向 AMQP 端点发布存储桶通知。

      默认值为 ``off``。

   .. tab-item:: 配置设置

      .. mc-conf:: notify_amqp

      用于定义 AMQP 服务端点以供 :ref:`MinIO 存储桶通知 <minio-bucket-notifications>` 使用的顶层配置键。

      使用 :mc-cmd:`mc admin config set` 设置或更新 AMQP 服务端点。
      对于每个目标，:mc-conf:`~notify_amqp.url` 参数都是*必需*的。
      以空白字符（``" "``）分隔的列表形式指定其他可选参数。

      .. code-block:: shell
         :class: copyable

         mc admin config set notify_amqp \ 
           url="amqp://user:password@endpoint:port" \
           [ARGUMENT="VALUE"] ...

URL
~~~

*必需*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_AMQP_URL

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: notify_amqp url
         :delimiter: " "

指定 MinIO 发布存储桶事件的 AMQP 服务器端点。
例如，``amqp://myuser:mypassword@localhost:5672``。

.. include:: /includes/linux/minio-server.rst
   :start-after: start-notify-target-online-desc
   :end-before: end-notify-target-online-desc

Exchange（交换机）
~~~~~~~~~~~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_AMQP_EXCHANGE

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: notify_amqp exchange
         :delimiter: " "

指定要使用的 AMQP exchange 名称。

Exchange 类型
~~~~~~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_AMQP_EXCHANGE_TYPE

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: notify_amqp exchange_type
         :delimiter: " "

指定 AMQP exchange 的类型。

路由键
~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_AMQP_ROUTING_KEY

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: notify_amqp routing_key
         :delimiter: " "

指定用于发布事件的 routing key。

Mandatory（强制）
~~~~~~~~~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_AMQP_MANDATORY

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: notify_amqp mandatory
         :delimiter: " "

指定 ``off`` 以忽略消息未送达错误。
默认值为 ``on``。

持久化
~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_AMQP_DURABLE

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: notify_amqp durable
         :delimiter: " "

指定 ``on`` 以在 broker 重启后保留消息队列。
默认值为 ``off``。

No Wait（不等待）
~~~~~~~~~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_AMQP_NO_WAIT

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: notify_amqp no_wait
         :delimiter: " "

指定 ``on`` 以启用非阻塞消息投递。
默认值为 ``off``。

Internal（内部）
~~~~~~~~~~~~~~~~

*可选*

.. tab-set::


   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_AMQP_INTERNAL

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: notify_amqp internal
         :delimiter: " "

.. 该说明目前不够清晰，后续需要进一步完善。

指定 ``on`` 以仅在 exchange 绑定到其他 exchanges 时使用该 exchange。
有关 AMQP exchange 绑定的更多信息，请参阅 RabbitMQ 文档中的 `Exchange to Exchange Bindings
<https://www.rabbitmq.com/e2e.html>`__。

自动删除
~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_AMQP_AUTO_DELETED

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: notify_amqp auto_deleted
         :delimiter: " "

指定 ``on`` 以在没有消费者时自动删除消息队列。
默认值为 ``off``。

投递模式
~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_AMQP_DELIVERY_MODE

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: notify_amqp delivery_mode
         :delimiter: " "

指定 ``1`` 以将投递模式设置为非持久化队列。

指定 ``2`` 以将投递模式设置为持久化队列。

队列目录
~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_AMQP_QUEUE_DIR

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: notify_amqp queue_dir
         :delimiter: " "

指定目录路径以启用 MinIO 对未送达消息的持久化事件存储，例如 ``/opt/minio/events``。

当 AMQP 服务离线时，MinIO 会将未送达事件存储在指定存储中，并在连接恢复后重放这些已存储事件。

队列限制
~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_AMQP_QUEUE_LIMIT

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: notify_amqp queue_limit
         :delimiter: " "

指定未送达消息的最大数量限制。
默认值为 ``100000``。

注释
~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_AMQP_COMMENT

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: notify_amqp comment
         :delimiter: " "

为 AMQP 配置指定注释。
