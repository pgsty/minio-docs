.. _minio-bucket-notifications-publish-mqtt:

======================
将事件发布到 MQTT
======================

.. default-domain:: minio

.. |ARN| replace:: ``arn:minio:sqs::primary:mqtt``

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 1

MinIO 支持将 :ref:`存储桶通知
<minio-bucket-notifications>` 事件发布到 `MQTT <https://www.mqtt.org/>`__
server/broker 端点。

向 MinIO 部署添加 MQTT 端点
------------------------------------------

以下过程会添加一个新的 MQTT 服务端点，以在 MinIO
部署中支持 :ref:`存储桶通知 <minio-bucket-notifications>`。

前提条件
~~~~~~~~~~~~~~

MQTT 3.1 或 3.1.1 服务器/代理
+++++++++++++++++++++++++++++++

此过程假定已存在一个 MQTT 3.1 或 3.1.1 server/broker，且 MinIO
部署能够连接到它。有关兼容 MQTT 的 server/broker 列表，请参见
`mqtt.org software listing <https://mqtt.org/software/>`__。

如果 MQTT 服务需要身份验证，则在配置过程中*必须*提供适当的用户名和密码，
以授予 MinIO 访问该服务的权限。

MinIO ``mc`` 命令行工具
++++++++++++++++++++++++++++++

此过程中的某些操作需要使用 :mc:`mc` 命令行工具。
安装说明请参见 ``mc`` :ref:`快速入门 <mc-install>`。

1) 将 MQTT 端点添加到 MinIO
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

你可以通过环境变量*或*运行时配置设置来配置新的 MQTT 服务端点。

.. tab-set::

   .. tab-item:: Environment Variables

      MinIO 支持使用
      :ref:`环境变量
      <minio-server-envvar-bucket-notification-mqtt>` 指定 MQTT 服务端点及其相关
      配置设置。:mc:`minio server` 进程会在下次启动时应用这些设置。
      
      以下示例代码设置了与配置 MQTT 服务端点相关的 *全部* 环境变量。
      *最少* 需要以下变量：

      - :envvar:`MINIO_NOTIFY_MQTT_ENABLE`
      - :envvar:`MINIO_NOTIFY_MQTT_BROKER`
      - :envvar:`MINIO_NOTIFY_MQTT_TOPIC`
      - :envvar:`MINIO_NOTIFY_MQTT_USERNAME` *如果 MQTT server/broker 强制要求身份验证/授权，则必需*
      - :envvar:`MINIO_NOTIFY_MQTT_PASSWORD` *如果 MQTT server/broker 强制要求身份验证/授权，则必需*

      .. cond:: windows
      
         .. code-block:: shell
            :class: copyable
         
               set MINIO_NOTIFY_MQTT_ENABLE_<IDENTIFIER>="on"
               set MINIO_NOTIFY_MQTT_BROKER_<IDENTIFIER>="ENDPOINT"
               set MINIO_NOTIFY_MQTT_TOPIC_<IDENTIFIER>="TOPIC"
               set MINIO_NOTIFY_MQTT_USERNAME_<IDENTIFIER>="<string>"
               set MINIO_NOTIFY_MQTT_PASSWORD_<IDENTIFIER>="<string>"
               set MINIO_NOTIFY_MQTT_QOS_<IDENTIFIER>="<string>"
               set MINIO_NOTIFY_MQTT_KEEP_ALIVE_INTERVAL_<IDENTIFIER>="<string>"
               set MINIO_NOTIFY_MQTT_RECONNECT_INTERVAL_<IDENTIFIER>="<string>"
               set MINIO_NOTIFY_MQTT_QUEUE_DIR_<IDENTIFIER>="<string>"
               set MINIO_NOTIFY_MQTT_QUEUE_LIMIT_<IDENTIFIER>="<string>"
               set MINIO_NOTIFY_MQTT_COMMENT_<IDENTIFIER>="<string>"

      .. cond:: not windows

         .. code-block:: shell
            :class: copyable

               export MINIO_NOTIFY_MQTT_ENABLE_<IDENTIFIER>="on"
               export MINIO_NOTIFY_MQTT_BROKER_<IDENTIFIER>="ENDPOINT"
               export MINIO_NOTIFY_MQTT_TOPIC_<IDENTIFIER>="TOPIC"
               export MINIO_NOTIFY_MQTT_USERNAME_<IDENTIFIER>="<string>"
               export MINIO_NOTIFY_MQTT_PASSWORD_<IDENTIFIER>="<string>"
               export MINIO_NOTIFY_MQTT_QOS_<IDENTIFIER>="<string>"
               export MINIO_NOTIFY_MQTT_KEEP_ALIVE_INTERVAL_<IDENTIFIER>="<string>"
               export MINIO_NOTIFY_MQTT_RECONNECT_INTERVAL_<IDENTIFIER>="<string>"
               export MINIO_NOTIFY_MQTT_QUEUE_DIR_<IDENTIFIER>="<string>"
               export MINIO_NOTIFY_MQTT_QUEUE_LIMIT_<IDENTIFIER>="<string>"
               export MINIO_NOTIFY_MQTT_COMMENT_<IDENTIFIER>="<string>"

      - 将 ``<IDENTIFIER>`` 替换为 MQTT 服务端点的唯一描述性字符串。
        对所有与新 MQTT 服务端点相关的环境变量都使用相同的 ``<IDENTIFIER>``
        值。以下示例假定标识符为 ``PRIMARY``。

        如果指定的 ``<IDENTIFIER>`` 与 MinIO 部署中现有的 MQTT 服务端点匹配，
        新设置将*覆盖*该端点的任何现有设置。使用
        :mc-cmd:`mc admin config get notify_mqtt <mc admin config get>`
        查看 MinIO 部署上当前配置的 MQTT 端点。

      - 将 ``<ENDPOINT>`` 替换为 MQTT 服务端点的 URL。例如：

        ``tcp://hostname:port``

      - 将 ``TOPIC`` 替换为 MQTT topic，MinIO 会将发布到 server/broker 的
        事件关联到该 topic。

      有关每个环境变量的完整文档，请参见 :ref:`用于存储桶通知的 MQTT 服务
      <minio-server-envvar-bucket-notification-mqtt>`。

   .. tab-item:: Configuration Settings

      MinIO 支持在运行中的 :mc:`minio server` 进程上使用
      :mc-cmd:`mc admin config set` 命令和 :mc-conf:`notify_mqtt`
      配置键添加或更新 MQTT 端点。你必须重启 :mc:`minio server`
      进程，才能应用任何新增或更新的配置设置。

      以下示例代码设置了与配置 MQTT 服务端点相关的*全部*设置。
      对于 MQTT server/broker 端点，以下配置设置是*最少*必需项：

      - :mc-conf:`~notify_mqtt.broker`
      - :mc-conf:`~notify_mqtt.topic`
      - :mc-conf:`~notify_mqtt.username` *如果 MQTT server/broker 强制要求身份验证/授权，则必需*
      - :mc-conf:`~notify_mqtt.password` *如果 MQTT server/broker 强制要求身份验证/授权，则必需*

      .. code-block:: shell
         :class: copyable

         mc admin config set ALIAS/ notify_mqtt:IDENTIFIER \
            broker="ENDPOINT" \
            topic="TOPIC" \
            username="username" \
            password="password" \
            qos="<integer>" \
            keep_alive_interval="60s|m|h|d"
            reconnect_interval="60s|m|h|d"
            queue_dir="<string>" \
            queue_limit="<string>" \
            comment="<string>"

      - 将 ``IDENTIFIER`` 替换为 MQTT 服务端点的唯一描述性字符串。
        本过程后续示例假定标识符为 ``PRIMARY``。

        如果指定的 ``IDENTIFIER`` 与 MinIO 部署中现有的 MQTT 服务端点匹配，
        新设置将*覆盖*该端点的任何现有设置。使用
        :mc-cmd:`mc admin config get notify_mqtt <mc admin config get>`
        查看 MinIO 部署上当前配置的 MQTT 端点。

      - 将 ``ENDPOINT`` 替换为 MQTT 服务端点的 URL。例如：

        ``tcp://hostname:port``

      - 将 ``TOPIC`` 替换为 MQTT topic，MinIO 会将发布到 server/broker 的
        事件关联到该 topic。

      有关每个设置的完整文档，请参见 :ref:`MQTT 存储桶通知配置设置
      <minio-server-config-bucket-notification-mqtt>`。

1) 重启 MinIO 部署
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

你必须重启 MinIO 部署以应用配置更改。
使用 :mc-cmd:`mc admin service restart` 命令重启该部署。

.. code-block:: shell
   :class: copyable

   mc admin service restart ALIAS

将 ``ALIAS`` 替换为要重启的部署的 :ref:`别名 <alias>`。

:mc:`minio server` 进程在启动时会为每个已配置的 MQTT
目标打印一行类似如下的内容：

.. code-block:: shell

   SQS ARNs: arn:minio:sqs::primary:mqtt

将关联的 MQTT 部署配置为目标时，你必须在配置存储桶通知时指定 ARN 资源。

.. include:: /includes/common-bucket-notifications.rst
   :start-after: start-bucket-notification-find-arn
   :end-before: end-bucket-notification-find-arn

1) 将 MQTT 端点配置为存储桶通知目标
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc:`mc event add` 命令添加新的存储桶通知事件，并将已配置的
MQTT 服务作为目标：

.. code-block:: shell
   :class: copyable

   mc event add ALIAS/BUCKET arn:minio:sqs::primary:mqtt \
     --event EVENTS

- 将 ``ALIAS`` 替换为 MinIO 部署的 :ref:`别名 <alias>`。
- 将 ``BUCKET`` 替换为要配置该事件的存储桶名称。
- 将 ``EVENTS`` 替换为以逗号分隔的 :ref:`事件
  <mc-event-supported-events>` 列表，MinIO 会为这些事件触发通知。

使用 :mc:`mc event ls` 查看给定通知目标已配置的所有存储桶事件：

.. code-block:: shell
   :class: copyable

   mc event ls ALIAS/BUCKET arn:minio:sqs::primary:MQTT

4) 验证已配置的事件
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

对已配置新事件的存储桶执行某个操作，并检查 MQTT 服务中的通知数据。
所需操作取决于配置存储桶通知时指定了哪些
:mc-cmd:`事件 <mc event add --event>`。

例如，如果存储桶通知配置包含 ``s3:ObjectCreated:Put`` 事件，
则可以使用 :mc:`mc cp` 命令在存储桶中创建新对象并触发通知。

.. code-block:: shell
   :class: copyable

   mc cp ~/data/new-object.txt ALIAS/BUCKET

更新 MinIO 部署中的 MQTT 端点
---------------------------------------------

以下过程会更新现有 MQTT 服务端点，以在 MinIO
部署中支持 :ref:`存储桶通知 <minio-bucket-notifications>`。

前提条件
~~~~~~~~~~~~~~

MQTT 3.1 或 3.1.1 服务器/代理端点
++++++++++++++++++++++++++++++++++++++++

此过程假定已存在一个 MQTT 3.1 或 3.1.1 server/broker，且 MinIO
部署能够连接到它。有关兼容 MQTT 的 server/broker 列表，请参见
`mqtt.org software listing <https://mqtt.org/software/>`__。

如果 MQTT 服务需要身份验证，则在配置过程中*必须*提供适当的用户名和密码，
以授予 MinIO 访问该服务的权限。

MinIO ``mc`` 命令行工具
++++++++++++++++++++++++++++++

此过程中的某些操作需要使用 :mc:`mc` 命令行工具。
安装说明请参见 ``mc`` :ref:`快速入门 <mc-install>`。


1) 列出部署中已配置的 MQTT 端点
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc admin config get` 命令列出该部署中当前已配置的 MQTT
服务端点：

.. code-block:: shell
   :class: copyable

   mc admin config get ALIAS/ notify_mqtt

将 ``ALIAS`` 替换为 MinIO 部署的 :ref:`别名 <alias>`。

命令输出类似如下：

.. code-block:: shell

   notify_mqtt:primary  broker="tcp://mqtt-primary.example.net:port" password="" queue_dir="" queue_limit="0" reconnect_interval="0s"  keep_alive_interval="0s" qos="0" topic="" username=""
   notify_mqtt:secondary  broker="tcp://mqtt-primary.example.net:port" password="" queue_dir="" queue_limit="0" reconnect_interval="0s"  keep_alive_interval="0s" qos="0" topic="" username=""

:mc-conf:`notify_mqtt` 键是
:ref:`minio-server-config-bucket-notification-mqtt` 的顶层配置键。
:mc-conf:`broker <notify_mqtt.broker>` 键为给定的 `notify_mqtt`
键指定 MQTT server/broker 端点。``notify_mqtt:<IDENTIFIER>``
后缀描述该 MQTT 服务端点的唯一标识符。

记下要更新的 MQTT 服务端点标识符，供下一步使用。

2) 更新 MQTT 端点
~~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc admin config set` 命令为 MQTT 服务端点设置新配置：

.. code-block:: shell
   :class: copyable

   mc admin config set ALIAS/ notify_mqtt:<IDENTIFIER> \
      url="MQTT://user:password@hostname:port" \
      exchange="<string>" \
      exchange_type="<string>" \
      routing_key="<string>" \
      mandatory="<string>" \
      durable="<string>" \
      no_wait="<string>" \
      internal="<string>" \
      auto_deleted="<string>" \
      delivery_mode="<string>" \
      queue_dir="<string>" \
      queue_limit="<string>" \
      comment="<string>"

以下配置设置是 MQTT server/broker 端点的*最少*必需项：

- :mc-conf:`~notify_mqtt.broker`
- :mc-conf:`~notify_mqtt.topic`
- :mc-conf:`~notify_mqtt.username` *如果 MQTT server/broker 强制要求身份验证/授权，则必需*
- :mc-conf:`~notify_mqtt.password` *如果 MQTT server/broker 强制要求身份验证/授权，则必需*

所有其他配置设置均为*可选*。有关 MQTT 配置设置的完整列表，请参见
:ref:`minio-server-config-bucket-notification-mqtt`。

3) 重启 MinIO 部署
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

你必须重启 MinIO 部署以应用配置更改。
使用 :mc-cmd:`mc admin service restart` 命令重启该部署。

.. code-block:: shell
   :class: copyable

   mc admin service restart ALIAS

将 ``ALIAS`` 替换为要重启的部署的 :ref:`别名 <alias>`。

:mc:`minio server` 进程在启动时会为每个已配置的 MQTT
目标打印一行类似如下的内容：

.. code-block:: shell

   SQS ARNs: arn:minio:sqs::primary:mqtt

3) 验证更改
~~~~~~~~~~~~~~~~~~~~~~~

对某个使用已更新 MQTT 服务端点进行事件配置的存储桶执行某个操作，
并检查 MQTT 服务中的通知数据。所需操作取决于配置存储桶通知时指定了哪些
:mc-cmd:`事件 <mc event add --event>`。

例如，如果存储桶通知配置包含 ``s3:ObjectCreated:Put`` 事件，
则可以使用 :mc:`mc cp` 命令在存储桶中创建新对象并触发通知。

.. code-block:: shell
   :class: copyable

   mc cp ~/data/new-object.txt ALIAS/BUCKET
