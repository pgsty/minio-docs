.. _minio-server-envvar-bucket-notification-nsq:
.. _minio-server-config-bucket-notification-nsq:

=========================
NSQ 通知设置
=========================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

本页面记录了将 NSQ 服务配置为 :ref:`存储桶通知 <minio-bucket-notifications>` 目标时所使用的设置。
有关如何使用这些设置的教程，请参阅 :ref:`minio-bucket-notifications-publish-nsq`。

.. include:: /includes/common-mc-admin-config.rst
   :start-after: start-minio-settings-defined
   :end-before: end-minio-settings-defined

.. include:: /includes/common-mc-admin-config.rst
   :start-after: start-minio-settings-test-before-prod
   :end-before: end-minio-settings-test-before-prod

多个 NSQ 目标
-------------

你可以通过在每组相关 NSQ 设置的顶层键末尾附加唯一标识符 ``_ID``，指定多个 NSQ 服务端点。
例如，以下命令分别将两个不同的 NSQ 服务端点设置为 ``PRIMARY`` 和 ``SECONDARY``：

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. code-block:: shell
         :class: copyable
      
         export MINIO_NOTIFY_NSQ_ENABLE_PRIMARY="on"
         export MINIO_NOTIFY_NSQ_NSQD_ADDRESS_PRIMARY="https://user:password@nsq-endpoint.example.net:9200"
         export MINIO_NOTIFY_NSQ_TOPIC_PRIMARY="bucketevents"
      
         export MINIO_NOTIFY_NSQ_ENABLE_SECONDARY="on"
         export MINIO_NOTIFY_NSQ_NSQD_ADDRESS_SECONDARY="https://user:password@nsq-endpoint.example.net:9200"
         export MINIO_NOTIFY_NSQ_TOPIC_SECONDARY="bucketevents"

   .. tab-item:: 配置项
      :sync: config

      .. code-block:: shell

         mc admin config set notify_nsq:primary \ 
            nsqd_address="ENDPOINT" \
            topic="<string>" \
            [ARGUMENT="VALUE"] ... \
   
         mc admin config set notify_nsq:secondary \
            nsqd_address="ENDPOINT" \
            topic="<string>" \
            [ARGUMENT="VALUE"] ... \

设置
----

启用
~~~~

*必填*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar
      
      .. envvar:: MINIO_NOTIFY_NSQ_ENABLE

      指定 ``on`` 以启用将存储桶通知发布到 NSQ 端点。

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: notify_nsq

      用于定义 NSQ server/broker 端点的顶层配置键，供 :ref:`MinIO 存储桶通知 <minio-bucket-notifications>` 使用。
   
      使用 :mc-cmd:`mc admin config set` 设置或更新 NSQ server/broker 端点。
      每个端点都*必须*包含以下参数：
      
      - :mc-conf:`~notify_nsq.nsqd_address`
      - :mc-conf:`~notify_nsq.topic`
   
      其他可选参数以空白字符（``" "``）分隔的列表形式指定。

      .. code-block:: shell
         :class: copyable
   
         mc admin config set notify_nsq                          \ 
            nsqd_address="https://nsq-endpoint.example.net:4150" \
            topic="<string>"                                     \
            [ARGUMENT="VALUE"] ...

NSQ Daemon 服务器地址
~~~~~~~~~~~~~~~~~~~~~

*必填*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_NSQ_NSQD_ADDRESS

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: notify_nsq nsqd_address
         :delimiter: " "

指定 NSQ Daemon 运行所在的 NSQ 服务器地址。
例如：

``https://nsq-endpoint.example.net:4150``

.. include:: /includes/linux/minio-server.rst
   :start-after: start-notify-target-online-desc
   :end-before: end-notify-target-online-desc

主题
~~~~

*必填*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_NSQ_TOPIC

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: notify_nsq topic
         :delimiter: " "

指定 MinIO 向 broker 发布事件时使用的 NSQ topic 名称。

TLS
~~~

*可选*

.. tab-set::
   
   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_NSQ_TLS

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: notify_nsq tls
         :delimiter: " "

指定 ``on`` 以启用到 NSQ service broker 的 TLS 连接。

TLS 跳过校验
~~~~~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_NSQ_TLS_SKIP_VERIFY

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: notify_nsq tls_skip_verify
         :delimiter: " "

启用或禁用对 NSQ service broker TLS 证书的 TLS 校验。

- 指定 ``on`` 可禁用 TLS 校验（默认）。
- 指定 ``off`` 可启用 TLS 校验。

队列目录
~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_NSQ_QUEUE_DIR

   .. tab-item:: 配置项
      :sync: config
   
      .. mc-conf:: notify_nsq queue_dir
         :delimiter: " "

指定目录路径以启用 MinIO 对未投递消息的持久化事件存储，例如 ``/opt/minio/events``。

当 NSQ server/broker 离线时，MinIO 会将未投递事件存储在指定存储中，并在连接恢复后重放这些已存储事件。


队列上限
~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar
      
      .. envvar:: MINIO_NOTIFY_NSQ_QUEUE_LIMIT

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: notify_nsq queue_limit
         :delimiter: " "

指定未投递消息的最大上限。
默认值为 ``100000``。

备注
~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_NSQ_COMMENT

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: notify_nsq comment
         :delimiter: " "

指定与 NSQ 配置关联的备注。
