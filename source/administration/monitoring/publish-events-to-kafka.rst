.. _minio-bucket-notifications-publish-kafka:

========================
将事件发布到 Kafka
========================

.. default-domain:: minio

.. |ARN| replace:: ``arn:minio:sqs::primary:kafka``

.. contents:: 目录
   :local:
   :depth: 1

MinIO 支持将 :ref:`bucket notification
<minio-bucket-notifications>` 事件发布到 `Kafka <https://kafka.apache.org/>`__
服务端点。

MinIO 依赖 :github:`Shopify/sarama` 项目实现 Kafka 连接能力，
并共享该项目对 Kafka 的支持。更多信息请参见
``sarama`` 的 :github:`Compatibility and API stability
<Shopify/sarama/#compatibility-and-api-stability>` 章节。

向 MinIO 部署添加 Kafka 端点
----------------------------

以下过程会在 MinIO 部署中添加一个新的 Kafka 服务端点，
用于支持 :ref:`bucket notifications <minio-bucket-notifications>`。

前提条件
~~~~~~~~

Kafka 最低版本与支持版本
++++++++++++++++++++++++

MinIO 依赖 :github:`Shopify/sarama` 项目实现 Kafka 连接能力，
并共享该项目对 Kafka 的支持。更多信息请参见
``sarama`` 的 :github:`Compatibility and API stability
<Shopify/sarama/#compatibility-and-api-stability>` 章节。

MinIO ``mc`` 命令行工具
+++++++++++++++++++++++++++++++++++++++

该过程的部分操作需要使用 :mc:`mc` 命令行工具。
安装说明请参见 ``mc`` :ref:`Quickstart <mc-install>`。

1) 向 MinIO 添加 Kafka 端点
~~~~~~~~~~~~~~~~~~~~~~~~~~~

你可以使用环境变量，*或* 通过设置运行时配置项，来配置新的 Kafka 服务端点。

.. tab-set::

   .. tab-item:: 环境变量

      MinIO 支持使用
      :ref:`environment variables
      <minio-server-envvar-bucket-notification-kafka>`
      指定 Kafka 服务端点及其相关配置项。
      :mc:`minio server` 进程会在下次启动时应用这些配置。

      以下示例代码设置了配置 Kafka 服务端点相关的 *全部* 环境变量。
      最低 *必需* 的变量是
      :envvar:`MINIO_NOTIFY_KAFKA_ENABLE` 和
      :envvar:`MINIO_NOTIFY_KAFKA_BROKERS`：

      .. cond:: windows

         .. code-block:: shell
            :class: copyable

               set MINIO_NOTIFY_KAFKA_ENABLE_<IDENTIFIER>="on"
               set MINIO_NOTIFY_KAFKA_BROKERS_<IDENTIFIER>="<ENDPOINT>"
               set MINIO_NOTIFY_KAFKA_TOPIC_<IDENTIFIER>="<string>"
               set MINIO_NOTIFY_KAFKA_SASL_USERNAME_<IDENTIFIER>="<string>"
               set MINIO_NOTIFY_KAFKA_SASL_PASSWORD_<IDENTIFIER>="<string>"
               set MINIO_NOTIFY_KAFKA_SASL_MECHANISM_<IDENTIFIER>="<string>"
               set MINIO_NOTIFY_KAFKA_TLS_CLIENT_AUTH_<IDENTIFIER>="<string>"
               set MINIO_NOTIFY_KAFKA_SASL_<IDENTIFIER>="<string>"
               set MINIO_NOTIFY_KAFKA_TLS_<IDENTIFIER>="<string>"
               set MINIO_NOTIFY_KAFKA_TLS_SKIP_VERIFY_<IDENTIFIER>="<string>"
               set MINIO_NOTIFY_KAFKA_CLIENT_TLS_CERT_<IDENTIFIER>="<string>"
               set MINIO_NOTIFY_KAFKA_CLIENT_TLS_KEY_<IDENTIFIER>="<string>"
               set MINIO_NOTIFY_KAFKA_QUEUE_DIR_<IDENTIFIER>="<string>"
               set MINIO_NOTIFY_KAFKA_QUEUE_LIMIT_<IDENTIFIER>="<string>"
               set MINIO_NOTIFY_KAFKA_VERSION_<IDENTIFIER>="<string>"
               set MINIO_NOTIFY_KAFKA_COMMENT_<IDENTIFIER>="<string>"

      .. cond:: not windows

         .. code-block:: shell
            :class: copyable

               export MINIO_NOTIFY_KAFKA_ENABLE_<IDENTIFIER>="on"
               export MINIO_NOTIFY_KAFKA_BROKERS_<IDENTIFIER>="<ENDPOINT>"
               export MINIO_NOTIFY_KAFKA_TOPIC_<IDENTIFIER>="<string>"
               export MINIO_NOTIFY_KAFKA_SASL_USERNAME_<IDENTIFIER>="<string>"
               export MINIO_NOTIFY_KAFKA_SASL_PASSWORD_<IDENTIFIER>="<string>"
               export MINIO_NOTIFY_KAFKA_SASL_MECHANISM_<IDENTIFIER>="<string>"
               export MINIO_NOTIFY_KAFKA_TLS_CLIENT_AUTH_<IDENTIFIER>="<string>"
               export MINIO_NOTIFY_KAFKA_SASL_<IDENTIFIER>="<string>"
               export MINIO_NOTIFY_KAFKA_TLS_<IDENTIFIER>="<string>"
               export MINIO_NOTIFY_KAFKA_TLS_SKIP_VERIFY_<IDENTIFIER>="<string>"
               export MINIO_NOTIFY_KAFKA_CLIENT_TLS_CERT_<IDENTIFIER>="<string>"
               export MINIO_NOTIFY_KAFKA_CLIENT_TLS_KEY_<IDENTIFIER>="<string>"
               export MINIO_NOTIFY_KAFKA_QUEUE_DIR_<IDENTIFIER>="<string>"
               export MINIO_NOTIFY_KAFKA_QUEUE_LIMIT_<IDENTIFIER>="<string>"
               export MINIO_NOTIFY_KAFKA_VERSION_<IDENTIFIER>="<string>"
               export MINIO_NOTIFY_KAFKA_COMMENT_<IDENTIFIER>="<string>"

      - 将 ``<IDENTIFIER>`` 替换为该 Kafka 服务端点的唯一描述性字符串。
        与新目标服务端点相关的所有环境变量都应使用相同的 ``<IDENTIFIER>`` 值。
        以下示例假定标识符为 ``PRIMARY``。

        如果指定的 ``<IDENTIFIER>`` 与 MinIO 部署中已有的 Kafka 服务端点匹配，
        新配置会 *覆盖* 该端点的现有配置。
        使用
        :mc-cmd:`mc admin config get notify_kafka <mc admin config get>`
        查看 MinIO 部署当前已配置的 Kafka 端点。

      - 将 ``<ENDPOINT>`` 替换为逗号分隔的 Kafka broker 列表。
        例如：

        ``"kafka1.example.com:2021,kafka2.example.com:2021"``

      有关每个环境变量的完整说明，请参见 :ref:`用于存储桶通知的 Kafka 服务
      <minio-server-envvar-bucket-notification-kafka>`。

   .. tab-item:: 配置设置

      MinIO 支持在运行中的 :mc:`minio server` 进程上，
      使用 :mc-cmd:`mc admin config set` 命令和
      :mc-conf:`notify_kafka` 配置键来新增或更新 Kafka 端点。
      你必须重启 :mc:`minio server` 进程，才能应用新增或更新后的配置项。

      以下示例代码设置了配置 Kafka 服务端点相关的 *全部* 配置项。
      最低 *必需* 的配置项是
      :mc-conf:`notify_kafka brokers <notify_kafka.brokers>`：

      .. code-block:: shell
         :class: copyable

         mc admin config set ALIAS/ notify_kafka:IDENTIFIER \
            brokers="<ENDPOINT>" \
            topic="<string>" \
            sasl_username="<string>" \
            sasl_password="<string>" \
            sasl_mechanism="<string>" \
            tls_client_auth="<string>" \
            tls="<string>" \
            tls_skip_verify="<string>" \
            client_tls_cert="<string>" \
            client_tls_key="<string>" \
            version="<string>" \
            queue_dir="<string>" \
            queue_limit="<string>" \
            comment="<string>"

      - 将 ``IDENTIFIER`` 替换为该 Kafka 服务端点的唯一描述性字符串。
        本过程中的以下示例假定标识符为 ``PRIMARY``。

        如果指定的 ``IDENTIFIER`` 与 MinIO 部署中已有的 Kafka 服务端点匹配，
        新配置会 *覆盖* 该端点的现有配置。
        使用
        :mc-cmd:`mc admin config get notify_kafka <mc admin config get>`
        查看 MinIO 部署当前已配置的 Kafka 端点。

      - 将 ``ENDPOINT`` 替换为逗号分隔的 Kafka broker 列表。
        例如：

        ``"kafka1.example.com:2021,kafka2.example.com:2021"``

      有关每个配置项的完整说明，请参见 :ref:`Kafka 存储桶通知配置项
      <minio-server-config-bucket-notification-kafka>`。

1) 重启 MinIO 部署
~~~~~~~~~~~~~~~~~~

你必须重启 MinIO 部署才能应用这些配置更改。
使用 :mc-cmd:`mc admin service restart` 命令重启该部署。

.. code-block:: shell
   :class: copyable

   mc admin service restart ALIAS

将 ``ALIAS`` 替换为要重启的部署的 :ref:`alias <alias>`。

:mc:`minio server` 进程在启动时会为每个已配置的 Kafka 目标打印一行输出，
类似如下：

.. code-block:: shell

   SQS ARNs: arn:minio:sqs::primary:kafka

在将关联的 Kafka 部署配置为目标时，
你必须在配置存储桶通知时指定该 ARN 资源。

.. include:: /includes/common-bucket-notifications.rst
   :start-after: start-bucket-notification-find-arn
   :end-before: end-bucket-notification-find-arn


3) 使用 Kafka 端点作为目标配置存储桶通知
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc:`mc event add` 命令新增存储桶通知事件，
并将已配置的 Kafka 服务作为目标：

.. code-block:: shell
   :class: copyable

   mc event add ALIAS/BUCKET arn:minio:sqs::primary:kafka \
     --event EVENTS

- 将 ``ALIAS`` 替换为 MinIO 部署的 :ref:`alias <alias>`。
- 将 ``BUCKET`` 替换为要配置该事件的存储桶名称。
- 将 ``EVENTS`` 替换为逗号分隔的 :ref:`events
  <mc-event-supported-events>` 列表，MinIO 会在这些事件发生时触发通知。

使用 :mc:`mc event ls` 查看给定通知目标上配置的所有存储桶事件：

.. code-block:: shell
   :class: copyable

   mc event ls ALIAS/BUCKET arn:minio:sqs::primary:kafka

4) 验证已配置的事件
~~~~~~~~~~~~~~~~~~~

对配置了新事件的存储桶执行某项操作，
然后在 Kafka 服务中检查通知数据。
所需操作取决于配置存储桶通知时指定了哪些
:mc-cmd:`events <mc event add --event>`。

例如，如果存储桶通知配置包含
``s3:ObjectCreated:Put`` 事件，则可以使用
:mc:`mc cp` 命令在存储桶中创建一个新对象，以触发通知。

.. code-block:: shell
   :class: copyable

   mc cp ~/data/new-object.txt ALIAS/BUCKET

更新 MinIO 部署中的 Kafka 端点
------------------------------

以下过程会更新 MinIO 部署中现有的 Kafka 服务端点，
用于支持 :ref:`bucket notifications <minio-bucket-notifications>`。

前提条件
~~~~~~~~~~

Kafka 最低版本与支持版本
++++++++++++++++++++++++

MinIO 依赖 :github:`Shopify/sarama` 项目实现 Kafka 连接能力，
并共享该项目对 Kafka 的支持。更多信息请参见
``sarama`` 的 :github:`Compatibility and API stability
<Shopify/sarama/#compatibility-and-api-stability>` 章节。

MinIO ``mc`` 命令行工具
+++++++++++++++++++++++++++++++++++++++

该过程的部分操作需要使用 :mc:`mc` 命令行工具。
安装说明请参见 ``mc`` :ref:`Quickstart <mc-install>`。


1) 列出部署中已配置的 Kafka 端点
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc admin config get` 命令列出部署中当前已配置的 Kafka 服务端点：

.. code-block:: shell
   :class: copyable

   mc admin config get ALIAS/ notify_kafka

将 ``ALIAS`` 替换为 MinIO 部署的 :ref:`alias <alias>`。

命令输出类似如下：

.. code-block:: shell

   notify_kafka:primary tls_skip_verify="off"  queue_dir="" queue_limit="0" sasl="off" sasl_password="" sasl_username="" tls_client_auth="0" tls="off" brokers="" topic="" client_tls_cert="" client_tls_key="" version=""
   notify_kafka:secondary tls_skip_verify="off"  queue_dir="" queue_limit="0" sasl="off" sasl_password="" sasl_username="" tls_client_auth="0" tls="off" brokers="" topic="" client_tls_cert="" client_tls_key="" version=""

:mc-conf:`notify_kafka` 键是
:ref:`minio-server-config-bucket-notification-kafka`
的顶层配置键。
:mc-conf:`brokers <notify_kafka.brokers>` 键指定给定 `notify_kafka` 键所对应的 Kafka 服务端点。
``notify_kafka:<IDENTIFIER>`` 后缀描述了该 Kafka 服务端点的唯一标识符。

记下你要在下一步中更新的 Kafka 服务端点标识符。

2) 更新 Kafka 端点
~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc admin config set` 命令为 Kafka 服务端点设置新配置：

.. code-block:: shell
   :class: copyable

   mc admin config set ALIAS/ notify_kafka:<IDENTIFIER> \
      brokers="https://kafka1.example.net:9200, https://kafka2.example.net:9200" \
      topic="<string>" \
      sasl_username="<string>" \
      sasl_password="<string>" \
      sasl_mechanism="<string>" \
      tls_client_auth="<string>" \
      tls="<string>" \
      tls_skip_verify="<string>" \
      client_tls_cert="<string>" \
      client_tls_key="<string>" \
      version="<string>" \
      queue_dir="<string>" \
      queue_limit="<string>" \
      comment="<string>"

:mc-conf:`notify_kafka brokers <notify_kafka.brokers>` 配置项
是 Kafka 服务端点的 *最低* 必需项。
所有其他配置项均为可选。
有关 Kafka 配置项的完整列表，请参见
:ref:`minio-server-config-bucket-notification-kafka`。

3) 重启 MinIO 部署
~~~~~~~~~~~~~~~~~~

你必须重启 MinIO 部署才能应用这些配置更改。
使用 :mc-cmd:`mc admin service restart` 命令重启该部署。

.. code-block:: shell
   :class: copyable

   mc admin service restart ALIAS

将 ``ALIAS`` 替换为要重启的部署的 :ref:`alias <alias>`。

:mc:`minio server` 进程在启动时会为每个已配置的 Kafka 目标打印一行输出，
类似如下：

.. code-block:: shell

   SQS ARNs: arn:minio:sqs::primary:kafka

4) 验证更改
~~~~~~~~~~~

对某个使用已更新 Kafka 服务端点配置了事件的存储桶执行某项操作，
然后在 Kafka 服务中检查通知数据。
所需操作取决于配置存储桶通知时指定了哪些
:mc-cmd:`events <mc event add --event>`。

例如，如果存储桶通知配置包含
``s3:ObjectCreated:Put`` 事件，则可以使用
:mc:`mc cp` 命令在存储桶中创建一个新对象，以触发通知。

.. code-block:: shell
   :class: copyable

   mc cp ~/data/new-object.txt ALIAS/BUCKET
