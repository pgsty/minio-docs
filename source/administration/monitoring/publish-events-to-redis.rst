.. _minio-bucket-notifications-publish-redis:

=====================
将事件发布到 Redis
=====================

.. default-domain:: minio

.. |ARN| replace:: ``arn:minio:sqs::primary:redis``

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 1

MinIO 支持将 :ref:`存储桶通知
<minio-bucket-notifications>` 事件发布到 `Redis <https://redis.io/>`__
服务端点。

向 MinIO 部署添加 Redis 端点
----------------------------

以下过程会在 MinIO 部署中添加一个新的 Redis 服务端点，用于支持
:ref:`存储桶通知 <minio-bucket-notifications>`。

前提条件
~~~~~~~~

MinIO ``mc`` 命令行工具
+++++++++++++++++++++++

此过程在部分操作中使用 :mc:`mc` 命令行工具。
安装说明请参见 ``mc`` :ref:`快速入门 <mc-install>`。

1) 将 Redis 端点添加到 MinIO
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

你可以使用环境变量 *或* 运行时配置设置来配置新的 Redis 服务端点。

.. tab-set::

   .. tab-item:: 环境变量

      MinIO 支持使用
      :ref:`环境变量
      <minio-server-envvar-bucket-notification-redis>` 指定 Redis 服务端点及其相关
      配置设置。:mc:`minio server` 进程会在下一次启动时应用这些设置。

      以下示例代码设置了与配置 Redis 服务端点相关的 *全部* 环境变量。
      最少的 *必需* 变量包括：

      - :envvar:`MINIO_NOTIFY_REDIS_ENABLE` 
      - :envvar:`MINIO_NOTIFY_REDIS_ADDRESS`
      - :envvar:`MINIO_NOTIFY_REDIS_KEY` 
      - :envvar:`MINIO_NOTIFY_REDIS_FORMAT`

      .. cond:: windows
      
         .. code-block:: shell
            :class: copyable
         
               set MINIO_NOTIFY_REDIS_ENABLE_<IDENTIFIER>="on"
               set MINIO_NOTIFY_REDIS_ADDRESS_<IDENTIFIER>="<ENDPOINT>"
               set MINIO_NOTIFY_REDIS_KEY_<IDENTIFIER>="<STRING>"
               set MINIO_NOTIFY_REDIS_FORMAT_<IDENTIFIER>="<string>"
               set MINIO_NOTIFY_REDIS_PASSWORD_<IDENTIFIER>="<string>"
               set MINIO_NOTIFY_REDIS_QUEUE_DIR_<IDENTIFIER>="<string>"
               set MINIO_NOTIFY_REDIS_QUEUE_LIMIT_<IDENTIFIER>="<string>"
               set MINIO_NOTIFY_REDIS_COMMENT_<IDENTIFIER>="<string>"

      .. cond:: not windows

         .. code-block:: shell
            :class: copyable

               export MINIO_NOTIFY_REDIS_ENABLE_<IDENTIFIER>="on"
               export MINIO_NOTIFY_REDIS_ADDRESS_<IDENTIFIER>="<ENDPOINT>"
               export MINIO_NOTIFY_REDIS_KEY_<IDENTIFIER>="<STRING>"
               export MINIO_NOTIFY_REDIS_FORMAT_<IDENTIFIER>="<string>"
               export MINIO_NOTIFY_REDIS_PASSWORD_<IDENTIFIER>="<string>"
               export MINIO_NOTIFY_REDIS_QUEUE_DIR_<IDENTIFIER>="<string>"
               export MINIO_NOTIFY_REDIS_QUEUE_LIMIT_<IDENTIFIER>="<string>"
               export MINIO_NOTIFY_REDIS_COMMENT_<IDENTIFIER>="<string>"

      - 将 ``<IDENTIFIER>`` 替换为目标服务端点的唯一描述性字符串。
        对于与新目标服务端点相关的所有环境变量，请使用相同的 ``<IDENTIFIER>`` 值。
        以下示例假定标识符为 ``PRIMARY``。

        如果指定的 ``<IDENTIFIER>`` 与 MinIO 部署上现有的 Redis 服务端点匹配，
        新设置会 *覆盖*
        该端点的所有现有设置。使用
        :mc-cmd:`mc admin config get notify_redis <mc admin config get>`
        查看当前在 MinIO 部署上已配置的 Redis 端点。

      - 将 ``<ENDPOINT>`` 替换为 Redis 服务端点的 URL。
        例如：``https://redis.example.com:6369``


      有关每个环境变量的完整文档，请参见
      :ref:`存储桶通知的 Redis 服务
      <minio-server-envvar-bucket-notification-redis>`。

   .. tab-item:: 配置设置

      MinIO 支持在正在运行的 :mc:`minio server` 进程上，使用
      :mc-cmd:`mc admin config set` 命令和 :mc-conf:`notify_redis` 配置键
      添加或更新 Redis 端点。你必须重启 :mc:`minio server` 进程，才能应用任何新的
      或更新后的配置设置。

      以下示例代码设置了与配置 Redis 服务端点相关的 *全部* 设置。
      最少的 *必需* 设置包括：

      - :mc-conf:`notify_redis address <notify_redis.address>`
      - :mc-conf:`notify_redis key <notify_redis.key>`
      - :mc-conf:`notify_redis format <notify_redis.format>`

      .. code-block:: shell
         :class: copyable

         mc admin config set ALIAS/ notify_redis:IDENTIFIER \
           address="ENDPOINT" \
           key="<string>" \
           format="<string>" \
           password="<string>" \
           queue_dir="<string>" \
           queue_limit="<string>" \
           comment="<string>"

      - 将 ``IDENTIFIER`` 替换为 Redis 服务端点的唯一描述性字符串。
        本过程中的以下示例假定标识符为 ``PRIMARY``。

        如果指定的 ``IDENTIFIER`` 与 MinIO 部署上现有的 Redis 服务端点匹配，
        新设置会 *覆盖*
        该端点的所有现有设置。使用
        :mc-cmd:`mc admin config get notify_redis <mc admin config get>`
        查看当前在 MinIO 部署上已配置的 Redis 端点。

      - 将 ``ENDPOINT`` 替换为 Redis 服务端点的 URL。
        例如：``https://redis.example.com:6369``

      有关每个设置的完整文档，请参见
      :ref:`Redis 存储桶通知配置设置
      <minio-server-config-bucket-notification-redis>`。

1) 重启 MinIO 部署
~~~~~~~~~~~~~~~~~~

你必须重启 MinIO 部署才能应用配置更改。
使用 :mc-cmd:`mc admin service restart` 命令重启该部署。

.. code-block:: shell
   :class: copyable

   mc admin service restart ALIAS

将 ``ALIAS`` 替换为要重启的部署的 :ref:`别名 <alias>`。

:mc:`minio server` 进程在启动时会为每个已配置的 Redis 目标打印一行类似如下的内容：

.. code-block:: shell

   SQS ARNs: arn:minio:sqs::primary:redis

将关联的 Redis 部署配置为目标来配置存储桶通知时，必须指定该 ARN 资源。

.. include:: /includes/common-bucket-notifications.rst
   :start-after: start-bucket-notification-find-arn
   :end-before: end-bucket-notification-find-arn

3) 将 Redis 端点配置为存储桶通知目标
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc:`mc event add` 命令新增一个存储桶通知事件，并将已配置的 Redis 服务
作为目标：

.. code-block:: shell
   :class: copyable

   mc event add ALIAS/BUCKET arn:minio:sqs::primary:redis \
     --event EVENTS

- 将 ``ALIAS`` 替换为某个 MinIO 部署的 :ref:`别名 <alias>`。
- 将 ``BUCKET`` 替换为要配置该事件的存储桶名称。
- 将 ``EVENTS`` 替换为一个以逗号分隔的 :ref:`事件
  <mc-event-supported-events>` 列表，MinIO 会为这些事件触发通知。

使用 :mc:`mc event ls` 查看给定通知目标的所有已配置存储桶事件：

.. code-block:: shell
   :class: copyable

   mc event ls ALIAS/BUCKET arn:minio:sqs::primary:redis

4) 验证已配置的事件
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

对已为其配置新事件的存储桶执行某个操作，并检查 Redis 服务中的通知数据。
所需的操作取决于在配置存储桶通知时指定了哪些
:mc-cmd:`事件 <mc event add --event>`。

例如，如果存储桶通知配置包含 ``s3:ObjectCreated:Put`` 事件，
则可以使用 :mc:`mc cp` 命令在存储桶中创建一个新对象并触发通知。

.. code-block:: shell
   :class: copyable

   mc cp ~/data/new-object.txt ALIAS/BUCKET

在 MinIO 部署中更新 Redis 端点
------------------------------

以下过程会更新 MinIO 部署中现有的 Redis 服务端点，以支持
:ref:`存储桶通知 <minio-bucket-notifications>`。

前提条件
~~~~~~~~

MinIO ``mc`` 命令行工具
+++++++++++++++++++++++

此过程在部分操作中使用 :mc:`mc` 命令行工具。
安装说明请参见 ``mc`` :ref:`快速入门 <mc-install>`。


1) 列出部署中已配置的 Redis 端点
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc admin config get` 命令列出部署中当前已配置的 Redis
服务端点：

.. code-block:: shell
   :class: copyable

   mc admin config get ALIAS/ notify_redis

将 ``ALIAS`` 替换为 MinIO 部署的 :ref:`别名 <alias>`。

命令输出类似如下：

.. code-block:: shell

   notify_redis:primary address="https://redis.example.com:6369" format="namespace" key="minioevent" password="" queue_dir="" queue_limit="0"
   notify_redis:secondary address="https://redis.example.com:6369" format="namespace" key="minioevent" password="" queue_dir="" queue_limit="0"

:mc-conf:`notify_redis` 键是
:ref:`minio-server-config-bucket-notification-redis` 的顶层配置键。
:mc-conf:`address <notify_redis.address>` 键为给定的 `notify_redis` 键指定 Redis
服务端点。``notify_redis:<IDENTIFIER>`` 后缀表示该 Redis 服务端点的唯一标识符。

记下你要更新的 Redis 服务端点标识符，以便在下一步中使用。

2) 更新 Redis 端点
~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc admin config set` 命令为 Redis 服务端点设置新配置：

.. code-block:: shell
   :class: copyable

   mc admin config set ALIAS/ notify_redis:IDENTIFIER \
      address="ENDPOINT" \
      key="<string>" \
      format="<string>" \
      password="<string>" \
      queue_dir="<string>" \
      queue_limit="<string>" \
      comment="<string>"

:mc-conf:`notify_redis address <notify_redis.address>` 配置设置是 Redis
服务端点 *最少* 必需的配置项。所有其他配置设置均为 *可选*。有关 Redis
配置设置的完整列表，请参见
:ref:`minio-server-config-bucket-notification-redis`。

3) 重启 MinIO 部署
~~~~~~~~~~~~~~~~~~

你必须重启 MinIO 部署才能应用配置更改。
使用 :mc-cmd:`mc admin service restart` 命令重启该部署。

.. code-block:: shell
   :class: copyable

   mc admin service restart ALIAS

将 ``ALIAS`` 替换为要重启的部署的 :ref:`别名 <alias>`。

:mc:`minio server` 进程在启动时会为每个已配置的 Redis 目标打印一行类似如下的内容：

.. code-block:: shell

   SQS ARNs: arn:minio:sqs::primary:redis

4) 验证更改
~~~~~~~~~~~

对某个已使用更新后的 Redis 服务端点进行事件配置的存储桶执行操作，并检查 Redis
服务中的通知数据。所需的操作取决于在配置存储桶通知时指定了哪些
:mc-cmd:`事件 <mc event add --event>`。

例如，如果存储桶通知配置包含 ``s3:ObjectCreated:Put`` 事件，
则可以使用 :mc:`mc cp` 命令在存储桶中创建一个新对象并触发通知。

.. code-block:: shell
   :class: copyable

   mc cp ~/data/new-object.txt ALIAS/BUCKET
