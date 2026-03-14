.. _minio-bucket-notifications-publish-amqp:

=================================
将事件发布到 AMQP (RabbitMQ)
=================================

.. default-domain:: minio

.. |ARN| replace:: ``arn:minio:sqs::primary:amqp``

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 1

MinIO 支持将 :ref:`bucket notification
<minio-bucket-notifications>` 事件发布到 `AMQP 0-9-1 <https://www.amqp.org/>`__
服务端点，例如 `RabbitMQ <https://www.rabbitmq.com>`__。

MinIO 依赖 :github:`streadway/amqp` 项目实现 AMQP 连接。该项目主要针对
`RabbitMQ <https://www.rabbitmq.com/>`__ 部署进行测试，但其他兼容
`AMQP 0-9-1 <https://www.amqp.org/>`__ 的服务也可能可以使用。本页中的步骤假定服务端点为使用 AMQP 0-9-1 协议的 RabbitMQ 部署。

向 MinIO 部署添加 AMQP 端点
---------------------------

以下步骤用于向 MinIO 部署添加一个新的 AMQP 服务端点，以支持
:ref:`bucket notifications <minio-bucket-notifications>`。

前提条件
~~~~~~~~

AMQP 0-9-1 服务端点
+++++++++++++++++++

MinIO 依赖 :github:`streadway/amqp` 项目实现 AMQP 连接。该项目主要针对
`RabbitMQ <https://www.rabbitmq.com/>`__ 部署进行测试，但其他兼容
`AMQP 0-9-1-compatible <https://www.amqp.org/>`__ 的服务也可能可以使用。
本步骤假定服务端点为使用 0-9-1 协议的 RabbitMQ 部署。

如果 AMQP 服务要求身份验证，则你必须在配置过程中提供相应的用户名和密码，以授权 MinIO 访问该服务。

MinIO ``mc`` 命令行工具
+++++++++++++++++++++++

本步骤在部分操作中使用 :mc:`mc` 命令行工具。
安装说明请参见 ``mc`` :ref:`Quickstart <mc-install>`。

1) 向 MinIO 添加 AMQP 端点
~~~~~~~~~~~~~~~~~~~~~~~~~~

你可以使用环境变量或运行时配置设置来配置新的 AMQP 服务端点。

.. tab-set::

   .. tab-item:: 环境变量

      MinIO 支持使用
      :ref:`environment variables
      <minio-server-envvar-bucket-notification-amqp>` 指定 AMQP 服务端点及其关联配置设置。
      :mc:`minio server` 进程会在下一次启动时应用这些设置。

      以下示例代码设置了配置 AMQP 服务端点相关的全部环境变量。
      必需的最小变量为
      :envvar:`MINIO_NOTIFY_AMQP_ENABLE` 和 :envvar:`MINIO_NOTIFY_AMQP_URL`：

      .. cond:: windows

         .. code-block:: shell
            :class: copyable

               set MINIO_NOTIFY_AMQP_ENABLE_<IDENTIFIER>="on"
               set MINIO_NOTIFY_AMQP_URL_<IDENTIFIER>="<ENDPOINT>"
               set MINIO_NOTIFY_AMQP_EXCHANGE_<IDENTIFIER>="<string>"
               set MINIO_NOTIFY_AMQP_EXCHANGE_TYPE_<IDENTIFIER>="<string>"
               set MINIO_NOTIFY_AMQP_ROUTING_KEY_<IDENTIFIER>="<string>"
               set MINIO_NOTIFY_AMQP_MANDATORY_<IDENTIFIER>="<string>"
               set MINIO_NOTIFY_AMQP_DURABLE_<IDENTIFIER>="<string>"
               set MINIO_NOTIFY_AMQP_NO_WAIT_<IDENTIFIER>="<string>"
               set MINIO_NOTIFY_AMQP_INTERNAL_<IDENTIFIER>="<string>"
               set MINIO_NOTIFY_AMQP_AUTO_DELETED_<IDENTIFIER>="<string>"
               set MINIO_NOTIFY_AMQP_DELIVERY_MODE_<IDENTIFIER>="<string>"
               set MINIO_NOTIFY_AMQP_QUEUE_DIR_<IDENTIFIER>="<string>"
               set MINIO_NOTIFY_AMQP_QUEUE_LIMIT_<IDENTIFIER>="<string>"
               set MINIO_NOTIFY_AMQP_COMMENT_<IDENTIFIER>="<string>"

      .. cond:: not windows

         .. code-block:: shell
            :class: copyable

               export MINIO_NOTIFY_AMQP_ENABLE_<IDENTIFIER>="on"
               export MINIO_NOTIFY_AMQP_URL_<IDENTIFIER>="<ENDPOINT>"
               export MINIO_NOTIFY_AMQP_EXCHANGE_<IDENTIFIER>="<string>"
               export MINIO_NOTIFY_AMQP_EXCHANGE_TYPE_<IDENTIFIER>="<string>"
               export MINIO_NOTIFY_AMQP_ROUTING_KEY_<IDENTIFIER>="<string>"
               export MINIO_NOTIFY_AMQP_MANDATORY_<IDENTIFIER>="<string>"
               export MINIO_NOTIFY_AMQP_DURABLE_<IDENTIFIER>="<string>"
               export MINIO_NOTIFY_AMQP_NO_WAIT_<IDENTIFIER>="<string>"
               export MINIO_NOTIFY_AMQP_INTERNAL_<IDENTIFIER>="<string>"
               export MINIO_NOTIFY_AMQP_AUTO_DELETED_<IDENTIFIER>="<string>"
               export MINIO_NOTIFY_AMQP_DELIVERY_MODE_<IDENTIFIER>="<string>"
               export MINIO_NOTIFY_AMQP_QUEUE_DIR_<IDENTIFIER>="<string>"
               export MINIO_NOTIFY_AMQP_QUEUE_LIMIT_<IDENTIFIER>="<string>"
               export MINIO_NOTIFY_AMQP_COMMENT_<IDENTIFIER>="<string>"


      - 将 ``<IDENTIFIER>`` 替换为该 AMQP 服务端点的唯一描述性字符串。与新 AMQP 服务端点相关的所有环境变量都应使用相同的 ``<IDENTIFIER>`` 值。以下示例假定标识符为 ``PRIMARY``。

        如果指定的 ``<IDENTIFIER>`` 与 MinIO 部署中现有的某个 AMQP 服务端点匹配，则新设置会覆盖该端点的任何现有设置。使用
        :mc-cmd:`mc admin config get notify_amqp <mc admin config get>` 查看 MinIO 部署上当前已配置的 AMQP 端点。

      - 将 ``<ENDPOINT>`` 替换为 AMQP 服务端点的 URL。
        例如：

        ``amqp://user:password@hostname:port``

      参见 :ref:`AMQP Service for 存储桶通知
      <minio-server-envvar-bucket-notification-amqp>`，获取每个环境变量的完整文档。

   .. tab-item:: 配置设置

      MinIO 支持在运行中的 :mc:`minio server` 进程上，使用 :mc-cmd:`mc admin config set` 命令和
      :mc-conf:`notify_amqp` 配置键来添加或更新 AMQP 端点。你必须重启
      :mc:`minio server` 进程，才能应用任何新增或更新的配置设置。

      以下示例代码设置了配置 AMQP 服务端点相关的全部设置。
      必需的最小设置为
      :mc-conf:`notify_amqp url <notify_amqp.url>`：

      .. code-block:: shell
         :class: copyable

         mc admin config set ALIAS/ notify_amqp:IDENTIFIER \
           url="ENDPOINT" \
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

      - 将 ``IDENTIFIER`` 替换为该 AMQP 服务端点的唯一描述性字符串。本步骤中的后续示例假定标识符为 ``PRIMARY``。

        如果指定的 ``IDENTIFIER`` 与 MinIO 部署中现有的某个 AMQP 服务端点匹配，则新设置会覆盖该端点的任何现有设置。使用
        :mc-cmd:`mc admin config get notify_amqp <mc admin config get>` 查看 MinIO 部署上当前已配置的 AMQP 端点。

      - 将 ``ENDPOINT`` 替换为 AMQP 服务端点的 URL。
        例如：

        ``amqp://user:password@hostname:port``

      参见 :ref:`AMQP Bucket Notification Configuration Settings
      <minio-server-config-bucket-notification-amqp>`，获取每个设置的完整文档。

1) 重启 MinIO 部署
~~~~~~~~~~~~~~~~~~

你必须重启 MinIO 部署以应用配置更改。
使用 :mc-cmd:`mc admin service restart` 命令重启部署。

.. code-block:: shell
   :class: copyable

   mc admin service restart ALIAS

将 ``ALIAS`` 替换为要重启的部署的 :ref:`alias <alias>`。

:mc:`minio server` 进程会在启动时为每个已配置的 AMQP 目标打印一行类似如下的内容：

.. code-block:: shell

   SQS ARNs: arn:minio:sqs::primary:amqp

在将关联的 AMQP 部署配置为目标时，你必须在 bucket notification 配置中指定该 ARN 资源。

.. include:: /includes/common-bucket-notifications.rst
   :start-after: start-bucket-notification-find-arn
   :end-before: end-bucket-notification-find-arn

3) 使用 AMQP 端点作为目标配置 存储桶通知
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc:`mc event add` 命令新增 bucket notification 事件，并将已配置的 AMQP 服务作为目标：

.. code-block:: shell
   :class: copyable

   mc event add ALIAS/BUCKET arn:minio:sqs::primary:amqp \
     --event EVENTS

- 将 ``ALIAS`` 替换为 MinIO 部署的 :ref:`alias <alias>`。
- 将 ``BUCKET`` 替换为要配置事件的存储桶名称。
- 将 ``EVENTS`` 替换为以逗号分隔的 :ref:`events
  <mc-event-supported-events>` 列表，MinIO 会在这些事件发生时触发通知。

使用 :mc:`mc event ls` 查看给定通知目标上已配置的所有 bucket 事件：

.. code-block:: shell
   :class: copyable

   mc event ls ALIAS/BUCKET arn:minio:sqs::primary:amqp

4) 验证已配置的事件
~~~~~~~~~~~~~~~~~~~

对配置了新事件的存储桶执行一个操作，并检查 AMQP 服务中的通知数据。所需的具体操作取决于配置 bucket notification 时指定了哪些 :mc-cmd:`events <mc event add --event>`。

例如，如果 bucket notification 配置包含 ``s3:ObjectCreated:Put`` 事件，则可以使用
:mc:`mc cp` 命令在该存储桶中创建一个新对象，并触发通知。

.. code-block:: shell
   :class: copyable

   mc cp ~/data/new-object.txt ALIAS/BUCKET



更新 MinIO 部署中的 AMQP 端点
-----------------------------

以下步骤用于更新 MinIO 部署中现有的 AMQP 服务端点，以支持
:ref:`bucket notifications <minio-bucket-notifications>`。

前提条件
~~~~~~~~

AMQP 0-9-1 服务端点
+++++++++++++++++++

MinIO 依赖 :github:`streadway/amqp` 项目实现 AMQP 连接。该项目主要针对
`RabbitMQ <https://www.rabbitmq.com/>`__ 部署进行测试，但其他兼容
`AMQP 0-9-1-compatible <https://www.amqp.org/>`__ 的服务也可能可以使用。
本步骤假定服务端点为 RabbitMQ 部署。

如果 AMQP 服务要求身份验证，则你必须在配置过程中提供相应的用户名和密码，以授权 MinIO 访问该服务。

MinIO ``mc`` 命令行工具
+++++++++++++++++++++++

本步骤在部分操作中使用 :mc:`mc` 命令行工具。
安装说明请参见 ``mc`` :ref:`Quickstart <mc-install>`。


1) 列出部署中已配置的 AMQP 端点
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc admin config get` 命令列出部署中当前已配置的 AMQP 服务端点：

.. code-block:: shell
   :class: copyable

   mc admin config get ALIAS/ notify_amqp

将 ``ALIAS`` 替换为 MinIO 部署的 :ref:`alias <alias>`。

命令输出类似如下：

.. code-block:: shell

   notify_amqp:primary delivery_mode="0" exchange_type="" no_wait="off" queue_dir="" queue_limit="0"  url="amqp://user:password@hostname:port" auto_deleted="off" durable="off" exchange="" internal="off" mandatory="off" routing_key=""
   notify_amqp:secondary delivery_mode="0" exchange_type="" no_wait="off" queue_dir="" queue_limit="0"  url="amqp://user:password@hostname:port" auto_deleted="off" durable="off" exchange="" internal="off" mandatory="off" routing_key=""

:mc-conf:`notify_amqp` 键是
:ref:`minio-server-config-bucket-notification-amqp` 的顶层配置键。
:mc-conf:`url <notify_amqp.url>` 键为给定的 `notify_amqp` 键指定 AMQP 服务端点。
``notify_amqp:<IDENTIFIER>`` 后缀表示该 AMQP 服务端点的唯一标识符。

记下你要更新的 AMQP 服务端点标识符，以供下一步使用。

2) 更新 AMQP 端点
~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc admin config set` 命令为该 AMQP 服务端点设置新配置：

.. code-block:: shell
   :class: copyable

   mc admin config set ALIAS/ notify_amqp:<IDENTIFIER> \
      url="amqp://user:password@hostname:port" \
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

:mc-conf:`notify_amqp url <notify_amqp.url>` 配置设置是 AMQP 服务端点所需的最小配置。其他所有配置设置都是可选的。参见 :ref:`minio-server-config-bucket-notification-amqp`，获取完整的 AMQP 配置设置列表。

3) 重启 MinIO 部署
~~~~~~~~~~~~~~~~~~

你必须重启 MinIO 部署以应用配置更改。
使用 :mc-cmd:`mc admin service restart` 命令重启部署。

.. code-block:: shell
   :class: copyable

   mc admin service restart ALIAS

将 ``ALIAS`` 替换为要重启的部署的 :ref:`alias <alias>`。

:mc:`minio server` 进程会在启动时为每个已配置的 AMQP 目标打印一行类似如下的内容：

.. code-block:: shell

   SQS ARNs: arn:minio:sqs::primary:amqp

4) 验证更改
~~~~~~~~~~~

对某个使用已更新 AMQP 服务端点配置事件的存储桶执行一个操作，并检查 AMQP 服务中的通知数据。所需的具体操作取决于配置 bucket notification 时指定了哪些 :mc-cmd:`events <mc event add --event>`。

例如，如果 bucket notification 配置包含 ``s3:ObjectCreated:Put`` 事件，则可以使用
:mc:`mc cp` 命令在该存储桶中创建一个新对象，并触发通知。

.. code-block:: shell
   :class: copyable

   mc cp ~/data/new-object.txt ALIAS/BUCKET
