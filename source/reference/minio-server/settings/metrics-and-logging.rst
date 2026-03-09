.. _minio-server-envvar-metrics-logging:

============================
指标与日志设置
============================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

本页面介绍用于控制 MinIO 指标与日志相关行为的设置。
更多信息请参见 :ref:`minio-metrics-and-alerts`。

这些设置用于将常规 :mc:`minio server` 日志和审计日志发布到 HTTP webhook。
更完整的文档请参见 :ref:`minio-logging`。

.. include:: /includes/common-mc-admin-config.rst
   :start-after: start-minio-settings-defined
   :end-before: end-minio-settings-defined

.. include:: /includes/common-mc-admin-config.rst
   :start-after: start-minio-settings-test-before-prod
   :end-before: end-minio-settings-test-before-prod

- :ref:`minio-server-envvar-logging-regular`
- :ref:`minio-server-envvar-logging-audit`
- :ref:`minio-server-envvar-logging-audit-kafka`

Prometheus 认证
-------------------------

此设置控制 MinIO 如何向 Prometheus 进行认证。

.. tab-set::

   .. tab-item:: 环境变量
      :selected:

      .. envvar:: MINIO_PROMETHEUS_AUTH_TYPE

   .. tab-item:: 配置项

      .. include:: /includes/common-mc-admin-config.rst
         :start-after: start-minio-settings-no-config-option
         :end-before: end-minio-settings-no-config-option

指定 Prometheus :ref:`抓取端点 <minio-metrics-and-alerts>` 的认证模式。

- ``jwt`` - *默认* MinIO 要求抓取客户端提供 JWT token 以认证请求。
   使用 :mc-cmd:`mc admin prometheus generate` 生成所需的 JWT bearer token。

- ``public`` MinIO 不要求抓取客户端对其请求进行认证。

.. _minio-server-envvar-logging-regular:
.. _minio-server-config-logging-regular:

服务器日志
-----------

以下部分介绍将 :mc:`minio server` 日志发布到 HTTP webhook 端点的 MinIO 配置设置。
有关这些设置的更完整文档和使用教程，请参见 :ref:`minio-logging-publish-server-logs`。

定义多个端点
~~~~~~~~~~~~~~~~~~~~~~~~~~~

你可以通过为每组相关日志环境变量追加唯一标识符 ``_ID``，将多个 webhook 端点指定为日志目标。
例如，以下设置定义了两个不同的服务器日志 webhook 端点：

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. code-block:: shell
         :class: copyable

         export MINIO_LOGGER_WEBHOOK_ENABLE_PRIMARY="on"
         export MINIO_LOGGER_WEBHOOK_AUTH_TOKEN_PRIMARY="TOKEN"
         export MINIO_LOGGER_WEBHOOK_ENDPOINT_PRIMARY="http://webhook-1.example.net"

         export MINIO_LOGGER_WEBHOOK_ENABLE_SECONDARY="on"
         export MINIO_LOGGER_WEBHOOK_AUTH_TOKEN_SECONDARY="TOKEN"
         export MINIO_LOGGER_WEBHOOK_ENDPOINT_SECONDARY="http://webhook-2.example.net"

   .. tab-item:: 配置项
      :sync: config

      .. code-block:: shell
         :class: copyable

         mc admin config set logger_webhook:primary \
            endpoint="http://webhook-01.example.net" [ARGUMENTS=VALUE ...]

         mc admin config set logger_webhook:secondary \
            endpoint="http://webhook-02.example.net" [ARGUMENTS=VALUE ...]

设置
~~~~~~~~

Enable
++++++

.. tab-set::

   .. tab-item:: 环境变量
      :selected:

      .. envvar:: MINIO_LOGGER_WEBHOOK_ENABLE

      指定 ``"on"`` 以启用将 :mc:`minio server` 日志发布到 HTTP webhook 端点。

      需要同时指定 :envvar:`MINIO_LOGGER_WEBHOOK_ENDPOINT`。

   .. tab-item:: 配置项

      .. mc-conf:: logger_webhook

      用于配置将日志发送到 HTTP webhook 端点的顶层配置键。


端点
++++++++

*必填*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_LOGGER_WEBHOOK_ENDPOINT

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: logger_webhook endpoint
         :delimiter: " "

webhook 的 HTTP 端点。

认证 Token
++++++++++

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_LOGGER_WEBHOOK_AUTH_TOKEN

      端点所需类型的认证 token。
      对于不需要认证的端点可省略。

      为支持多种 token 类型，MinIO 会使用*完全按原样指定*的值构造请求认证头。
      具体端点可能要求你附加额外信息。

      例如：对于 Bearer token，请添加前缀 ``Bearer``：

      .. code-block:: shell
         :class: copyable

         export MINIO_LOGGER_WEBHOOK_AUTH_TOKEN_myendpoint="Bearer 1a2b3c4f5e"

      请根据端点要求调整该值。
      自定义认证格式可能类似如下：

      .. code-block:: shell
         :class: copyable

         export MINIO_LOGGER_WEBHOOK_AUTH_TOKEN_xyz="ServiceXYZ 1a2b3c4f5e"

      详情请参阅目标服务的文档。

      该环境变量对应 :mc-conf:`logger_webhook auth_token <logger_webhook.auth_token>` 配置项。

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: logger_webhook auth_token
         :delimiter: " "

         端点所需类型的认证 token。
         对于不需要认证的端点可省略。

         为支持多种 token 类型，MinIO 会使用*完全按原样指定*的值构造请求认证头。
         具体端点可能要求你附加额外信息。

         例如：对于 Bearer token，请添加前缀 ``Bearer``：

         .. code-block:: shell
            :class: copyable

               mc admin config set myminio logger_webhook   \
                  endpoint="https://webhook-1.example.net"  \
                  auth_token="Bearer 1a2b3c4f5e"

         请根据端点要求调整该值。
         自定义认证格式可能类似如下：

         .. code-block:: shell
            :class: copyable

               mc admin config set myminio logger_webhook   \
                  endpoint="https://webhook-1.example.net"  \
                  auth_token="ServiceXYZ 1a2b3c4f5e"

         详情请参阅目标服务的文档。

批大小
++++++++++

.. versionadded:: MinIO Server RELEASE.2024-03-10T02-53-48Z

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_LOGGER_WEBHOOK_BATCH_SIZE

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: logger_webhook batch_size
         :delimiter: " "

按批次收集并发送指定数量的事件到 webhook。
如果未设置，MinIO 每个请求发送一个事件。

客户端证书
++++++++++++++++++

*可选*

还需要同时设置 *Client Key*。

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_LOGGER_WEBHOOK_CLIENT_CERT

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: logger_webhook client_cert
         :delimiter: " "

用于向 webhook logger 认证的 mTLS 证书路径。

客户端密钥
++++++++++

*可选*

如果定义了 *Client Certificate*，则为必填。

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_LOGGER_WEBHOOK_CLIENT_KEY

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: logger_webhook client_key
         :delimiter: " "

用于向 webhook logger 服务认证的 mTLS 证书密钥路径。

代理
+++++

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_LOGGER_WEBHOOK_PROXY

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: logger_webhook proxy
         :delimiter: " "

      .. versionadded:: MinIO RELEASE.2023-02-22T18-23-45Z

定义在 MinIO 与外部 webhook 通信时供 webhook logger 使用的代理。

队列目录
+++++++++++++++

*可选*

.. versionadded:: RELEASE.2023-05-18T00-05-36Z

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_LOGGER_WEBHOOK_QUEUE_DIR

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: logger_webhook queue_dir
         :delimiter: " "

指定目录路径（例如 ``/opt/minio/events``）以启用 MinIO 对未投递消息的持久事件存储。
MinIO 进程必须对指定目录具有读取、写入和列举权限。

当 webhook 服务离线时，MinIO 会将未投递事件存储到指定存储中，并在连接恢复后回放这些事件。

队列大小
++++++++++

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_LOGGER_WEBHOOK_QUEUE_SIZE

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: logger_webhook queue_size
         :delimiter: " "

用于 logger webhook 目标队列大小的整数值。

.. _minio-server-envvar-logging-audit:
.. _minio-server-config-logging-audit:

Webhook 审计日志
------------------

以下部分介绍用于将审计日志发布到 HTTP webhook 端点的 MinIO 环境变量。
有关这些环境变量的更完整文档和使用教程，请参见 :ref:`minio-logging-publish-audit-logs`。

多个目标
~~~~~~~~~~~~~~~~

你可以通过为每组相关日志设置追加唯一标识符 ``_ID``，将多个 webhook 端点指定为审计日志目标。

例如，以下命令设置了两个不同的审计日志 webhook 端点：

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. code-block:: shell
         :class: copyable

         export MINIO_AUDIT_WEBHOOK_ENABLE_PRIMARY="on"
         export MINIO_AUDIT_WEBHOOK_AUTH_TOKEN_PRIMARY="TOKEN"
         export MINIO_AUDIT_WEBHOOK_ENDPOINT_PRIMARY="http://webhook-1.example.net"
         export MINIO_AUDIT_WEBHOOK_CLIENT_CERT_SECONDARY="/tmp/cert.pem"
         export MINIO_AUDIT_WEBHOOK_CLIENT_KEY_SECONDARY="/tmp/key.pem"

         export MINIO_AUDIT_WEBHOOK_ENABLE_SECONDARY="on"
         export MINIO_AUDIT_WEBHOOK_AUTH_TOKEN_SECONDARY="TOKEN"
         export MINIO_AUDIT_WEBHOOK_ENDPOINT_SECONDARY="http://webhook-1.example.net"
         export MINIO_AUDIT_WEBHOOK_CLIENT_CERT_SECONDARY="/tmp/cert.pem"
         export MINIO_AUDIT_WEBHOOK_CLIENT_KEY_SECONDARY="/tmp/key.pem"

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: audit_webhook

         用于定义 HTTP webhook 目标并发布 :ref:`MinIO audit logs <minio-logging>` 的顶层配置键。

         使用 :mc-cmd:`mc admin config set` 设置或更新 HTTP webhook 目标。
         以空格（``" "``）分隔列表的形式指定其他可选参数。

         .. code-block:: shell
            :class: copyable

            mc admin config set audit_webhook \
               endpoint="http://webhook.example.net" [ARGUMENTS=VALUE ...]

         你可以通过在顶层键后追加 ``[:name]`` 来指定多个 HTTP webhook 目标。
         例如，以下命令分别将两个不同的 HTTP webhook 目标设置为 ``primary`` 和 ``secondary``：

         .. code-block:: shell
            :class: copyable

            mc admin config set audit_webhook:primary \
               endpoint="http://webhook-01.example.net" [ARGUMENTS=VALUE ...]


            mc admin config set audit_webhook:secondary \
               endpoint="http://webhook-02.example.net" [ARGUMENTS=VALUE ...]

设置
~~~~~~~~

Enable
++++++

.. tab-set::

   .. tab-item:: 环境变量
      :selected:

      .. envvar:: MINIO_AUDIT_WEBHOOK_ENABLE

         指定 ``"on"`` 以启用向 HTTP webhook 端点发布审计日志。

         需要同时指定 :envvar:`MINIO_AUDIT_WEBHOOK_ENDPOINT`。

   .. tab-item:: 配置项

      配置一个 audit webhook 即表示启用该目标。
      不存在单独的 ``enable`` 配置项。

端点
++++++++

*必填*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_AUDIT_WEBHOOK_ENDPOINT

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: audit_webhook endpoint
         :delimiter: " "

webhook 的 HTTP 端点。

认证 Token
++++++++++

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_AUDIT_WEBHOOK_AUTH_TOKEN

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: audit_webhook auth_token
         :delimiter: " "

端点所需类型的认证 token。
对于不需要认证的端点可省略。

为支持多种 token 类型，MinIO 会使用*完全按原样指定*的值构造请求认证头。
具体端点可能要求你附加额外信息。

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      例如，对于 Bearer token，请添加前缀 ``Bearer``：

      .. code-block:: shell
         :class: copyable

         export MINIO_AUDIT_WEBHOOK_AUTH_TOKEN_myendpoint="Bearer 1a2b3c4f5e"

      请根据端点要求调整该值。

      自定义认证格式可能类似如下：

      .. code-block:: shell
         :class: copyable

         export MINIO_AUDIT_WEBHOOK_AUTH_TOKEN_xyz="ServiceXYZ 1a2b3c4f5e"

   .. tab-item:: 配置项
      :sync: config

      .. code-block:: shell
         :class: copyable

         mc admin config set myminio audit_webhook       \
                  endpoint="http://webhook.example.net"  \
                  auth_token="Bearer 1a2b3c4f5e"

      请根据端点要求调整该值。

      自定义认证格式的命令可能类似如下：

      .. code-block:: shell
         :class: copyable

         mc admin config set myminio audit_webhook       \
                  endpoint="http://webhook.example.net"  \
                  auth_token="ServiceXYZ 1a2b3c4f5e"

详情请参阅目标服务的文档。

批大小
++++++++++

.. versionadded:: MinIO Server RELEASE.2024-03-10T02-53-48Z

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_AUDIT_WEBHOOK_BATCH_SIZE


   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: audit_webhook batch_size
         :delimiter: " "

按批次收集并发送指定数量的事件到 webhook。
如果未设置，MinIO 每个请求发送一个事件。

客户端证书
++++++++++++++++++

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_AUDIT_WEBHOOK_CLIENT_CERT

      还需要同时指定 :envvar:`MINIO_AUDIT_WEBHOOK_CLIENT_KEY`。

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: audit_webhook client_cert
         :delimiter: " "

      还需要同时指定 :mc-conf:`~audit_webhook.client_key`。

提交给 HTTP webhook 的 x.509 客户端证书。
对于不要求客户端提供已知 TLS 证书的 webhook 可省略。

客户端密钥
++++++++++

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_AUDIT_WEBHOOK_CLIENT_KEY

      还需要同时指定 :envvar:`MINIO_AUDIT_WEBHOOK_CLIENT_CERT`。

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: audit_webhook client_key
         :delimiter: " "

      需要指定 :mc-conf:`~audit_webhook.client_cert`。

提交给 HTTP webhook 的 x.509 私钥。
对于不要求客户端提供已知 TLS 证书的 webhook 可省略。


队列目录
+++++++++++++++

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_AUDIT_WEBHOOK_QUEUE_DIR

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: audit_webhook queue_dir
         :delimiter: " "

.. versionadded:: RELEASE.2023-05-18T00-05-36Z

指定目录路径（例如 ``/opt/minio/events``）以启用 MinIO 对未投递消息的持久事件存储。
MinIO 进程必须对指定目录具有读取、写入和列举权限。

当 webhook 服务离线时，MinIO 会将未投递事件存储到指定存储中，并在连接恢复后回放这些事件。

队列大小
++++++++++

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_AUDIT_WEBHOOK_QUEUE_SIZE

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: audit_webhook queue_size
         :delimiter: " "

用于 audit webhook 目标队列大小的整数值。
默认值为 ``100000`` 个事件。

.. _minio-server-envvar-logging-audit-kafka:
.. _minio-server-config-logging-kafka-audit:

Kafka 审计日志
----------------

以下部分介绍用于将审计日志发布到 Kafka broker 的 MinIO 环境变量。


.. mc-conf:: audit_kafka

   用于定义 Kafka broker 目标并发布 :ref:`MinIO audit logs <minio-logging>` 的顶层配置键。

   使用 :mc-cmd:`mc admin config set` 设置或更新 Kafka 审计目标。
   以空格（``" "``）分隔列表的形式指定其他可选参数。

   .. code-block:: shell
      :class: copyable

      mc admin config set audit_kafka \
         brokers="https://kafka-endpoint.example.net:9092" [ARGUMENTS=VALUE ...]


设置
~~~~~~~~

Enable
++++++

*必填*

.. tab-set::

   .. tab-item:: 环境变量
      :selected:

      .. envvar:: MINIO_AUDIT_KAFKA_ENABLE

      设置为 ``"on"`` 以启用该目标。

      设置为 ``"off"`` 以禁用该目标。

   .. tab-item:: 配置项

      该值没有对应的配置项。
      使用环境变量可禁用已配置的 audit webhook 目标。

Brokers
+++++++

*必填*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_AUDIT_KAFKA_BROKERS

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: audit_kafka brokers
         :delimiter: " "

Kafka broker 地址的逗号分隔列表：

.. code-block:: shell

   brokers="https://kafka-1.example.net:9092,https://kafka-2.example.net:9092"

至少必须有一个 broker 在线且 MinIO server 可达，才能初始化并发送审计日志事件。
MinIO 会按指定顺序检查每个 broker。

Topic
+++++

*必填*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_AUDIT_KAFKA_TOPIC

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: audit_kafka topic
         :delimiter: " "

与 MinIO 审计日志事件关联的 Kafka topic 名称。

TLS
+++

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_AUDIT_KAFKA_TLS

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: audit_kafka tls
         :delimiter: " "

设置为 ``"on"`` 以启用到指定 Kafka brokers 的 TLS 连接。

默认值为 ``"off"``。

TLS Skip Verify
+++++++++++++++

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_AUDIT_KAFKA_TLS_SKIP_VERIFY

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: audit_kafka tls_skip_verify
         :delimiter: " "

设置为 ``"on"`` 以指示 MinIO 跳过对 Kafka broker TLS 证书的校验。

你可以使用该选项连接使用未知签发方 TLS 证书的 Kafka brokers，例如自签名证书或企业内部 CA（Certificate Authorities）签发的证书。

默认情况下，MinIO 会同时使用系统信任库*以及* MinIO :ref:`CA directory <minio-tls>` 中的内容来校验远端客户端 TLS 证书。

默认值为 ``"off"``，即严格校验 TLS 证书。

SASL
++++

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_AUDIT_KAFKA_SASL

      需要指定 :envvar:`MINIO_AUDIT_KAFKA_SASL_USERNAME` 和 :envvar:`MINIO_AUDIT_KAFKA_SASL_PASSWORD`。

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: audit_kafka sasl
         :delimiter: " "

      需要指定 :mc-conf:`~audit_kafka.sasl_username` 和 :mc-conf:`~audit_kafka.sasl_password`。

设置为 ``"on"`` 以指示 MinIO 使用 SASL 对 Kafka brokers 进行认证。

SASL Username
+++++++++++++

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_AUDIT_KAFKA_SASL_USERNAME

      需要指定 :envvar:`MINIO_AUDIT_KAFKA_SASL` 和 :envvar:`MINIO_AUDIT_KAFKA_SASL_PASSWORD`。

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: audit_kafka sasl_username
         :delimiter: " "

      需要指定 :mc-conf:`~audit_kafka.sasl` 和 :mc-conf:`~audit_kafka.sasl_password`。

MinIO 用于对 Kafka brokers 进行认证的 SASL 用户名。

SASL Password
+++++++++++++

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_AUDIT_KAFKA_SASL_PASSWORD

      需要指定 :envvar:`MINIO_AUDIT_KAFKA_SASL` 和 :envvar:`MINIO_AUDIT_KAFKA_SASL_USERNAME`。

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: audit_kafka sasl_password
         :delimiter: " "

      需要指定 :mc-conf:`~audit_kafka.sasl` 和 :mc-conf:`~audit_kafka.sasl_username`。

MinIO 用于对 Kafka brokers 进行认证的 SASL 密码。

SASL Mechanism
++++++++++++++

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_AUDIT_KAFKA_SASL_MECHANISM

      .. important::

         ``PLAIN`` 认证机制会以明文形式在网络中传输凭据。
         使用 :envvar:`MINIO_AUDIT_KAFKA_TLS` 以启用到 Kafka brokers 的 TLS 连接，并确保 SASL 凭据安全传输。

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: audit_kafka sasl_mechanism
         :delimiter: " "

      .. important::

         ``PLAIN`` 认证机制会以明文形式在网络中传输凭据。
         使用 :mc-conf:`~audit_kafka.tls` 以启用到 Kafka brokers 的 TLS 连接，并确保 SASL 凭据安全传输。

MinIO 用于对 Kafka brokers 进行认证的 SASL 机制。

默认值为 ``plain``。

TLS  Client Auth
++++++++++++++++

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_AUDIT_KAFKA_TLS_CLIENT_AUTH

      需要指定 :envvar:`MINIO_AUDIT_KAFKA_CLIENT_TLS_CERT` 和 :envvar:`MINIO_AUDIT_KAFKA_CLIENT_TLS_KEY`。

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: audit_kafka tls_client_auth
         :delimiter: " "

      需要指定 :mc-conf:`~audit_kafka.client_tls_cert` 和 :mc-conf:`~audit_kafka.client_tls_key`。

设置为 ``"on"`` 以指示 MinIO 使用 mTLS 对 Kafka brokers 进行认证。

Client TLS Certificate
++++++++++++++++++++++

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_AUDIT_KAFKA_CLIENT_TLS_CERT

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: audit_kafka client_tls_cert
         :delimiter: " "

用于 mTLS 认证的 TLS 客户端证书路径。

Client TLS Key
++++++++++++++

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_AUDIT_KAFKA_CLIENT_TLS_KEY

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: audit_kafka client_tls_key
         :delimiter: " "

用于 mTLS 认证的 TLS 客户端私钥路径。

Version
+++++++

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_AUDIT_KAFKA_VERSION

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: audit_kafka version
         :delimiter: " "

MinIO 在指定端点期望的 Kafka broker 版本。

如果 Kafka broker 版本与此设置指定的不匹配，MinIO 会返回错误。

Comment
+++++++

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_AUDIT_KAFKA_COMMENT

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: audit_kafka comment
         :delimiter: " "

与该配置关联的注释。

队列目录
+++++++++++++++

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_AUDIT_KAFKA_QUEUE_DIR

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: audit_kafka queue_dir
         :delimiter: " "

指定目录路径（例如 ``/opt/minio/events``）以启用 MinIO 对未投递消息的持久事件存储。

当 Kafka 服务离线时，MinIO 会将未投递事件存储到指定存储中，并在连接恢复后回放这些事件。

队列大小
++++++++++

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_AUDIT_KAFKA_QUEUE_SIZE

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: audit_kafka queue_size
         :delimiter: " "

指定未投递消息的最大上限。
默认值为 ``100000``。
