.. _minio-server-envvar-bucket-notification-webhook-service:
.. _minio-server-envvar-bucket-notification-webhook:
.. _minio-server-config-bucket-notification-webhook:

=====================================
Webhook 服务通知设置
=====================================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

本页记录了将 Webhook 服务配置为 :ref:`存储桶通知 <minio-bucket-notifications>` 目标所需的设置。
有关如何使用这些设置的教程，请参见 :ref:`minio-bucket-notifications-publish-webhook`。

.. include:: /includes/common-mc-admin-config.rst
   :start-after: start-minio-settings-defined
   :end-before: end-minio-settings-defined

.. include:: /includes/common-mc-admin-config.rst
   :start-after: start-minio-settings-test-before-prod
   :end-before: end-minio-settings-test-before-prod

多个 Webhook 服务目标
---------------------

可通过在顶层键后为每组相关 Webhook 设置追加唯一标识符 ``_ID`` 来指定多个 Webhook 服务端点。
例如，以下命令分别将两个不同的 Webhook 服务端点设置为 ``PRIMARY`` 和 ``SECONDARY``：

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. code-block:: shell
         :class: copyable
      
         export MINIO_NOTIFY_WEBHOOK_ENABLE_PRIMARY="on"
         export MINIO_NOTIFY_WEBHOOK_ENDPOINT_PRIMARY="https://webhook1.example.net"
      
         export MINIO_NOTIFY_WEBHOOK_ENABLE_SECONDARY="on"
         export MINIO_NOTIFY_WEBHOOK_ENDPOINT_SECONDARY="https://webhook1.example.net"

   .. tab-item:: 配置设置
      :sync: config

      .. code-block:: shell
   
         mc admin config set notify_webhook:primary \ 
            endpoint="https://webhook1.example.net"
            [ARGUMENT=VALUE ...]
   
         mc admin config set notify_webhook:secondary \
            endpoint="https://webhook2.example.net
            [ARGUMENT=VALUE ...]

设置
----

启用
~~~~

*必需*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_WEBHOOK_ENABLE

      指定 ``on`` 以启用将存储桶通知发布到 Webhook 服务端点。

      默认为 ``off``。

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: notify_webhook

      用于定义 Webhook 服务端点的顶层配置键，可用于 :ref:`MinIO 存储桶通知 <minio-bucket-notifications>`。
   
      使用 :mc-cmd:`mc admin config set` 设置或更新 Webhook 服务端点。
      :mc-conf:`~notify_webhook.endpoint` 参数对每个目标均为 *必需*。
      将其他可选参数指定为以空白字符（``" "``）分隔的列表。
   
      .. code-block:: shell
         :class: copyable
   
         mc admin config set notify_webhook \ 
           endpoint="https://webhook.example.net"
           [ARGUMENT="VALUE"] ... \

端点
~~~~

*必需*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_WEBHOOK_ENDPOINT

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: notify_webhook endpoint
         :delimiter: " "

指定 Webhook 服务的 URL。

.. include:: /includes/linux/minio-server.rst
   :start-after: start-notify-target-online-desc
   :end-before: end-notify-target-online-desc

认证令牌
~~~~~~~~

*必需*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_WEBHOOK_AUTH_TOKEN

      适用于该端点类型的认证令牌。
      对于不需要认证的端点可省略。
   
      为支持多种令牌类型，MinIO 会使用*完全按所给内容*的值来构造请求认证头。
      根据端点要求，你可能需要包含额外信息。
   
      例如，对于 Bearer 令牌，请在前面加上 ``Bearer``：
   
      .. code-block:: shell
         :class: copyable
   
         export MINIO_NOTIFY_WEBHOOK_AUTH_TOKEN_myendpoint="Bearer 1a2b3c4f5e"
   
      请根据端点要求调整该值。
      自定义认证格式可能类似如下：
   
      .. code-block:: shell
         :class: copyable
   
         export MINIO_NOTIFY_WEBHOOK_AUTH_TOKEN_xyz="ServiceXYZ 1a2b3c4f5e"
   
      更多详情请查阅目标服务的文档。

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: notify_webhook auth_token
         :delimiter: " "
   
         适用于该端点类型的认证令牌。
         对于不需要认证的端点可省略。
   
         为支持多种令牌类型，MinIO 会使用*完全按所给内容*的值来构造请求认证头。
         根据端点要求，你可能需要包含额外信息。
   
         例如，对于 Bearer 令牌，请在前面加上 ``Bearer``：
   
         .. code-block:: shell
            :class: copyable
   
               mc admin config set myminio notify_webhook   \
   	       endpoint="https://webhook-1.example.net"  \
                  auth_token="Bearer 1a2b3c4f5e"
   
         请根据端点要求调整该值。
         自定义认证格式可能类似如下：
   
         .. code-block:: shell
            :class: copyable
   
               mc admin config set myminio notify_webhook   \
                  endpoint="https://webhook-1.example.net"  \
                  auth_token="ServiceXYZ 1a2b3c4f5e"
   
         更多详情请查阅目标服务的文档。
   
         .. versionchanged:: RELEASE.2023-06-23T20-26-00Z
   
         当该值作为 :mc-cmd:`mc admin config get` 的返回内容的一部分时，MinIO 会将其打码。

队列目录
~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_WEBHOOK_QUEUE_DIR

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: notify_webhook queue_dir
         :delimiter: " "

指定目录路径以启用 MinIO 针对未投递消息的持久化事件存储，例如 ``/opt/minio/events``。

当 Webhook 服务离线时，MinIO 会将未投递事件存储到指定存储中，并在连接恢复后重放这些已存储事件。

队列上限
~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_WEBHOOK_QUEUE_LIMIT

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: notify_webhook queue_limit
         :delimiter: " "

指定未投递消息的最大上限。
默认为 ``100000``。

客户端证书
~~~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_WEBHOOK_CLIENT_CERT

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: notify_webhook client_cert
         :delimiter: " "

指定用于向 Webhook 服务执行 mTLS 认证的客户端证书路径。

客户端密钥
~~~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_WEBHOOK_CLIENT_KEY

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: notify_webhook client_key
         :delimiter: " "

指定用于向 Webhook 服务执行 mTLS 认证的客户端私钥路径。
