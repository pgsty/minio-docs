.. _minio-server-envvar-bucket-notification-nats:
.. _minio-server-config-bucket-notification-nats:

====================
NATS 通知设置
====================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. admonition:: NATS Streaming 已弃用
   :class: important

   NATS Streaming 已弃用。
   请迁移到 `JetStream <https://docs.nats.io/nats-concepts/jetstream>`__。

   相关的 MinIO 配置选项和环境变量也已弃用。

本页面说明了将 NATS 服务配置为 :ref:`存储桶通知 <minio-bucket-notifications>` 目标的设置。
有关如何使用这些设置的教程，请参见 :ref:`minio-bucket-notifications-publish-nats`。

.. include:: /includes/common-mc-admin-config.rst
   :start-after: start-minio-settings-defined
   :end-before: end-minio-settings-defined

.. include:: /includes/common-mc-admin-config.rst
   :start-after: start-minio-settings-test-before-prod
   :end-before: end-minio-settings-test-before-prod

多个 NATS 目标
----------------

你可以在顶层键后追加唯一标识符 ``_ID``，为每组相关的 NATS 设置指定多个 NATS 服务端点。

示例
~~~~

例如，以下命令分别将两个不同的 NATS 服务端点设置为 ``PRIMARY`` 和 ``SECONDARY``：

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. code-block:: shell
         :class: copyable

         export MINIO_NOTIFY_NATS_ENABLE_PRIMARY="on"
         export MINIO_NOTIFY_NATS_ADDRESS_PRIMARY="nats-endpoint.example.net:4222"

         export MINIO_NOTIFY_NATS_ENABLE_SECONDARY="on"
         export MINIO_NOTIFY_NATS_ADDRESS_SECONDARY="nats-endpoint.example.net:4222"

      在这些设置中，:envvar:`MINIO_NOTIFY_NATS_ENABLE_PRIMARY <MINIO_NOTIFY_NATS_ENABLE>` 表示该环境变量关联到 ID 为 ``PRIMARY`` 的 NATS 服务端点。

   .. tab-item:: 配置项
      :sync: config

      .. code-block:: shell

         mc admin config set notify_nats:primary \ 
            address="nats-endpoint.example.com:4222" \
            subject="minioevents" \ 
            [ARGUMENT=VALUE ...]

         mc admin config set notify_nats:secondary \
            address="nats-endpoint.example.com:4222" \
            subject="minioevents" \ 
            [ARGUMENT=VALUE ...]

设置
----

启用
~~~~

*必填*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_NATS_ENABLE

      指定 ``on`` 以启用向 NATS 服务端点发布存储桶通知。

      默认为 ``off``。

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: notify_nats

      用于定义 NATS 服务端点并将其用于 :ref:`MinIO bucket notifications <minio-bucket-notifications>` 的顶层配置键。

      使用 :mc-cmd:`mc admin config set` 设置或更新 NATS 服务端点。
      对于每个目标，:mc-conf:`~notify_nats.address` 和 :mc-conf:`~notify_nats.subject` 参数均为*必填*。
      其他可选参数请使用空白字符（``" "``）分隔的列表指定。

      .. code-block:: shell
         :class: copyable

         mc admin config set notify_nats \ 
           address="nats-endpoint.example.com:4222" \
           subject="minioevents" \
           [ARGUMENT="VALUE"] ... \

地址
~~~~

*必填*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_NATS_ADDRESS

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: notify_nats address
         :delimiter: " "

指定 MinIO 发布存储桶事件到的 NATS 服务端点。
例如：``nats-endpoint.example.com:4222``。

.. include:: /includes/linux/minio-server.rst
   :start-after: start-notify-target-online-desc
   :end-before: end-notify-target-online-desc

Subject
~~~~~~~

*必填*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_NATS_SUBJECT

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: notify_nats subject
         :delimiter: " "

指定 MinIO 在 NATS 端点上关联已发布事件的订阅。

用户名
~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_NATS_USERNAME

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: notify_nats username
         :delimiter: " "

指定连接到 NATS 服务端点时使用的用户名。

密码
~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_NATS_PASSWORD

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: notify_nats password
         :delimiter: " "

指定连接到 NATS 服务端点时使用的密码。

.. versionchanged:: RELEASE.2023-06-23T20-26-00Z

   MinIO 在作为 :mc-cmd:`mc admin config get` 返回结果的一部分时会对该值进行脱敏。

Token
~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_NATS_TOKEN

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: notify_nats token
         :delimiter: " "

指定连接到 NATS 服务端点时使用的 token。

.. versionchanged:: RELEASE.2023-06-23T20-26-00Z

   MinIO 在作为 :mc-cmd:`mc admin config get` 返回结果的一部分时会对该值进行脱敏。

用户凭证文件
~~~~~~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_NATS_USER_CREDENTIALS

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: notify_nats user_credentials
         :delimiter: " "

指定用于连接 NATS 服务端点的 `user credentials file <https://docs.nats.io/using-nats/developer/connecting/creds>`。

TLS
~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_NATS_TLS

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: notify_nats tls
         :delimiter: " "

指定 ``on`` 以启用到 NATS 服务端点的 TLS 连接。

TLS Skip Verify
~~~~~~~~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_NATS_TLS_SKIP_VERIFY

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: notify_nats tls_skip_verify
         :delimiter: " "

启用或禁用对 NATS 服务端点 TLS 证书的 TLS 校验。

- 指定 ``on`` 以禁用 TLS 校验（默认）。
- 指定 ``off`` 以启用 TLS 校验。

Ping Interval
~~~~~~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_NATS_PING_INTERVAL

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: notify_nats ping_interval
         :delimiter: " "

指定客户端向 NATS 服务器发送 ping 的时间间隔。
MinIO 支持以下时间单位：

- ``s`` - 秒，``"60s"``
- ``m`` - 分钟，``"5m"``
- ``h`` - 小时，``"1h"``
- ``d`` - 天，``"1d"``

Jetstream
~~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_NATS_JETSTREAM

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: notify_nats jetstream
         :delimiter: " "

指定 ``on`` 以启用 JetStream 支持，将事件流式传输到 NATS JetStream 服务端点。

Streaming
~~~~~~~~~

*已弃用*

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_NATS_STREAMING

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: notify_nats streaming
         :delimiter: " "

指定 ``on`` 以启用向 NATS 服务端点异步发布事件。

Streaming Async
~~~~~~~~~~~~~~~

*已弃用*

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_NATS_STREAMING_ASYNC

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: notify_nats streaming_async
         :delimiter: " "

指定 ``on`` 以启用向 NATS 服务端点异步发布事件。

Max ACK Responses In Flight
~~~~~~~~~~~~~~~~~~~~~~~~~~~

*已弃用*

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_NATS_STREAMING_MAX_PUB_ACKS_IN_FLIGHT

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: notify_nats streaming_max_pub_acks_in_flight
         :delimiter: " "

指定在等待 NATS 服务端点 ACK 响应之前可发布的消息数量。

Streaming Cluster ID
~~~~~~~~~~~~~~~~~~~~

*已弃用*

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_NATS_STREAMING_CLUSTER_ID

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: notify_nats streaming_cluster_id
         :delimiter: " "

指定 NATS Streaming 集群的唯一 ID。

Cert Authority
~~~~~~~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_NATS_CERT_AUTHORITY

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: notify_nats cert_authority
         :delimiter: " "

指定用于签署 NATS 服务端点 TLS 证书的 Certificate Authority 证书链路径。

Client Cert
~~~~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_NATS_CLIENT_CERT

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: notify_nats client_cert
         :delimiter: " "

指定用于执行到 NATS 服务端点 mTLS 认证的客户端证书路径。

Client Key
~~~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_NATS_CLIENT_KEY

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: notify_nats client_key
         :delimiter: " "

指定用于执行到 NATS 服务端点 mTLS 认证的客户端私钥路径。

Queue Directory
~~~~~~~~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_NATS_QUEUE_DIR

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: notify_nats queue_dir
         :delimiter: " "

指定目录路径以启用 MinIO 对未投递消息的持久化事件存储，例如 ``/opt/minio/events``。

当 NATS server/broker 离线时，MinIO 会将未投递事件存储在指定存储中，并在连接恢复后重放已存储事件。

Queue Limit
~~~~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_NATS_QUEUE_LIMIT

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: notify_nats queue_limit
         :delimiter: " "

指定未投递消息的最大上限。
默认为 ``100000``。

Comment
~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_NATS_COMMENT

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: notify_nats comment
         :delimiter: " "

指定与 NATS 配置关联的注释。
