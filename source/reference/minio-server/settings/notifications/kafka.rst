.. _minio-server-envvar-bucket-notification-kafka:
.. _minio-server-config-bucket-notification-kafka:

===================
Kafka 通知设置
===================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

本页面记录了将 Kafka 服务配置为 :ref:`Bucket Notifications <minio-bucket-notifications>` 目标的相关设置。
有关如何使用这些设置的教程，请参见 :ref:`minio-bucket-notifications-publish-kafka`。

.. include:: /includes/common-mc-admin-config.rst
   :start-after: start-minio-settings-defined
   :end-before: end-minio-settings-defined

.. include:: /includes/common-mc-admin-config.rst
   :start-after: start-minio-settings-test-before-prod
   :end-before: end-minio-settings-test-before-prod

多个 Kafka 目标
----------------

你可以在顶层键后附加唯一标识符 ``_ID``，为每组相关的 Kafka 设置指定多个 Kafka 服务端点。

示例
~~~~

例如，以下命令分别将两个不同的 Kafka 服务端点设置为 ``PRIMARY`` 和 ``SECONDARY``：

.. tab-set:: 
   
   .. tab-item:: Environment Variable
      :sync: envvar

      .. code-block:: shell
         :class: copyable
      
         export MINIO_NOTIFY_KAFKA_ENABLE_PRIMARY="on"
         export MINIO_NOTIFY_KAFKA_BROKERS_PRIMARY="https://kafka1.example.net:9200, https://kafka2.example.net:9200"
      
         export MINIO_NOTIFY_KAFKA_ENABLE_SECONDARY="on"
         export MINIO_NOTIFY_KAFKA_BROKERS_SECONDARY="https://kafka1.example.net:9200, https://kafka2.example.net:9200"

   .. tab-item:: Configuration Setting
      :sync: config

      .. code-block:: shell

         mc admin config set notify_kafka:primary \ 
            brokers="https://kafka1.example.net:9200, https://kafka2.example.net:9200"
            [ARGUMENT=VALUE ...]

         mc admin config set notify_kafka:secondary \
            brokers="https://kafka1.example.net:9200, https://kafka2.example.net:9200"
            [ARGUMENT=VALUE ...]

      请注意，对于配置设置，唯一标识符只附加到 ``notify_kafka``，而不是附加到每个单独参数。

设置
----

启用
~~~~

*必需*

.. tab-set::

   .. tab-item:: Environment Variable
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_KAFKA_ENABLE

      指定 ``on`` 以启用将存储桶通知发布到 Kafka 服务端点。

      默认为 ``off``。

   .. tab-item:: Configuration Setting
      :sync: config

      .. mc-conf:: notify_kafka

      用于定义 Kafka 服务端点并与 :ref:`MinIO bucket notifications <minio-bucket-notifications>` 配合使用的顶层配置键。

      使用 :mc-cmd:`mc admin config set` 设置或更新 Kafka 服务端点。
      对于每个目标，:mc-conf:`~notify_kafka.brokers` 参数都是*必需*的。
      其他可选参数请以空白字符（``" "``）分隔的列表形式指定。

      .. code-block:: shell
         :class: copyable

         mc admin config set notify_kafka \ 
           brokers="https://kafka1.example.net:9200, https://kafka2.example.net:9200"
           [ARGUMENT="VALUE"] ... \

Brokers
~~~~~~~

*必需*

.. tab-set::

   .. tab-item:: Environment Variable
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_KAFKA_BROKERS

   .. tab-item:: Configuration Setting
      :sync: config

      .. mc-conf:: notify_kafka brokers
         :delimiter: " "

指定以逗号分隔的 Kafka broker 地址列表。
例如：

``"kafka1.example.com:2021,kafka2.example.com:2021"``

.. include:: /includes/linux/minio-server.rst
   :start-after: start-notify-target-online-desc
   :end-before: end-notify-target-online-desc

Topic
~~~~~

*可选*

.. tab-set::

   .. tab-item:: Environment Variable
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_KAFKA_TOPIC

   .. tab-item:: Configuration Setting
      :sync: config

      .. mc-conf:: notify_kafka topic
         :delimiter: " "

指定 MinIO 发布存储桶事件的 Kafka topic 名称。

SASL
~~~~

*可选*

.. tab-set::

   .. tab-item:: Environment Variable
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_KAFKA_SASL

   .. tab-item:: Configuration Setting
      :sync: config

      .. mc-conf:: notify_kafka sasl
         :delimiter: " "

指定 ``on`` 以启用 SASL 身份验证。

SASL Username
~~~~~~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: Environment Variable
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_KAFKA_SASL_USERNAME

   .. tab-item:: Configuration Setting
      :sync: config

      .. mc-conf:: notify_kafka sasl_username
         :delimiter: " "

指定用于对 Kafka broker 执行 SASL/PLAIN 或 SASL/SCRAM 身份验证的用户名。

SASL Password
~~~~~~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: Environment Variable
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_KAFKA_SASL_PASSWORD

   .. tab-item:: Configuration Setting
      :sync: config

      .. mc-conf:: notify_kafka sasl_password
         :delimiter: " "

指定用于对 Kafka broker 执行 SASL/PLAIN 或 SASL/SCRAM 身份验证的密码。

.. versionchanged:: RELEASE.2023-06-23T20-26-00Z

   当此值作为 :mc-cmd:`mc admin config get` 返回结果的一部分时，MinIO 会对其进行脱敏。

SASL Mechanism
~~~~~~~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: Environment Variable
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_KAFKA_SASL_MECHANISM

   .. tab-item:: Configuration Setting
      :sync: config

      .. mc-conf:: notify_kafka sasl_mechanism
         :delimiter: " "

指定用于对 Kafka broker 进行身份验证的 SASL 机制。
MinIO 支持以下机制：

- ``PLAIN``（默认）
- ``SHA256``
- ``SHA512``

TLS Client Auth
~~~~~~~~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: Environment Variable
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_KAFKA_TLS_CLIENT_AUTH

   .. tab-item:: Configuration Setting
      :sync: config

      .. mc-conf:: notify_kafka tls_client_auth
         :delimiter: " "

指定 Kafka broker 的客户端身份验证类型。
下表列出了支持的取值及其映射关系

.. list-table::
   :header-rows: 1
   :widths: 20 80
   :width: 100%

   * - 值
     - 身份验证类型

   * - 0
     - ``NoClientCert``

   * - 1
     - ``RequestClientCert``

   * - 2
     - ``RequireAnyClientCert``

   * - 3
     - ``VerifyClientCertIfGiven``

   * - 4
     - ``RequireAndVerifyClientCert``

有关各客户端身份验证类型的更多信息，请参见 `ClientAuthType <https://golang.org/pkg/crypto/tls/#ClientAuthType>`__。

TLS
~~~

*可选*

.. tab-set::

   .. tab-item:: Environment Variable
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_KAFKA_TLS

   .. tab-item:: Configuration Setting
      :sync: config

      .. mc-conf:: notify_kafka tls
         :delimiter: " "

指定 ``on`` 以启用与 Kafka broker 的 TLS 连接。

TLS Skip Verify
~~~~~~~~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: Environment Variable
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_KAFKA_TLS_SKIP_VERIFY

   .. tab-item:: Configuration Setting
      :sync: config

      .. mc-conf:: notify_kafka tls_skip_verify
         :delimiter: " "

启用或禁用对 NATS 服务端点 TLS 证书的 TLS 验证。

- 指定 ``on`` 以禁用 TLS 验证（*默认*）。
- 指定 ``off`` 以启用 TLS 验证。

Client TLS Cert
~~~~~~~~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: Environment Variable
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_KAFKA_CLIENT_TLS_CERT

   .. tab-item:: Configuration Setting
      :sync: config

      .. mc-conf:: notify_kafka client_tls_cert
         :delimiter: " "

指定客户端证书路径，用于对 Kafka broker 执行 mTLS 身份验证。

Client TLS Key
~~~~~~~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: Environment Variable
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_KAFKA_CLIENT_TLS_KEY

   .. tab-item:: Configuration Setting
      :sync: config

      .. mc-conf:: notify_kafka client_tls_key
         :delimiter: " "

指定客户端私钥路径，用于对 Kafka broker 执行 mTLS 身份验证。

Version
~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: Environment Variable
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_KAFKA_VERSION

   .. tab-item:: Configuration Setting
      :sync: config

      .. mc-conf:: notify_kafka version
         :delimiter: " "

指定在对 Kafka 集群执行操作时假定的 Kafka 集群版本。
有关此字段行为的更多信息，请参见 `sarama reference documentation <https://github.com/shopify/sarama/blob/v1.20.1/config.go#L327>`__。

Batch Size
~~~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: Environment Variable
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_KAFKA_BATCH_SIZE

   .. tab-item:: Configuration Setting
      :sync: config

      .. mc-conf:: notify_kafka batch_size
         :delimiter: " "

指定整数值，作为向 Kafka 发送记录时的 `batch size <https://kafka.apache.org/documentation/#producerconfigs_batch.size>`__。

.. versionchanged:: RELEASE.2023-12-02T10-51-33Z

   MinIO 先前将此值限制为 ``100``。

Queue Directory
~~~~~~~~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: Environment Variable
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_KAFKA_QUEUE_DIR

   .. tab-item:: Configuration Setting
      :sync: config

      .. mc-conf:: notify_kafka queue_dir
         :delimiter: " "

指定目录路径以启用 MinIO 对未投递消息的持久化事件存储，例如 ``/opt/minio/events``。

当 Kafka server/broker 离线时，MinIO 会将未投递事件存储在指定存储中，并在连接恢复后重放这些已存储事件。

Queue Limit
~~~~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: Environment Variable
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_KAFKA_QUEUE_LIMIT

   .. tab-item:: Configuration Setting
      :sync: config

      .. mc-conf:: notify_kafka queue_limit
         :delimiter: " "

指定未投递消息的最大限制。
默认为 ``100000``。

Comment
~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: Environment Variable
      :sync: envvar
      
      .. envvar:: MINIO_NOTIFY_KAFKA_COMMENT

   .. tab-item:: Configuration Setting
      :sync: config

      .. mc-conf:: notify_kafka comment
         :delimiter: " "

指定与 Kafka 配置关联的注释。

Compression Codec
~~~~~~~~~~~~~~~~~

.. versionadded:: MinIO Server RELEASE.2023-12-09T18-17-51Z

*可选*

.. tab-set::

   .. tab-item:: Environment Variable
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_KAFKA_PRODUCER_COMPRESSION_CODEC

   .. tab-item:: Configuration Setting
      :sync: config

      .. mc-conf:: notify_kafka compression_codec
         :delimiter: " "

指定向 Kafka 发送记录时使用的压缩编解码器。

支持以下取值：

- ``none``
- ``snappy``
- ``gzip``
- ``lz4``
- ``zstd``

Compression Level
~~~~~~~~~~~~~~~~~

.. versionadded:: MinIO Server RELEASE.2023-12-09T18-17-51Z

*可选*

.. tab-set::

   .. tab-item:: Environment Variable
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_KAFKA_PRODUCER_COMPRESSION_LEVEL

   .. tab-item:: Configuration Setting
      :sync: config

      .. mc-conf:: notify_kafka compression_level
         :delimiter: " "

根据已配置的压缩编解码器控制应用的压缩级别。

指定大于或等于 ``0`` 的整数值。
该值的效果取决于所选编解码器。
