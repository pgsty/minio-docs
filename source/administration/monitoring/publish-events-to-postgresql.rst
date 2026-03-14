.. _minio-bucket-notifications-publish-postgresql:

============================
将事件发布到 PostgreSQL
============================

.. default-domain:: minio

.. |ARN| replace:: ``arn:minio:sqs::primary:postgresql``

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 1

.. |postgresql-uri-reference| replace:: `PostgreSQL 连接字符串 <https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-CONNSTRING>`__

MinIO 支持将 :ref:`存储桶通知
<minio-bucket-notifications>` 事件发布到
`PostgreSQL <https://www.postgresql.org/>`__。MinIO 仅支持
PostgreSQL 9.5 及以上版本。

向 MinIO 部署添加 PostgreSQL 端点
------------------------------------------------

以下过程将为 MinIO 部署新增一个 PostgreSQL 服务端点，以支持
:ref:`存储桶通知 <minio-bucket-notifications>`。

前提条件
~~~~~~~~

PostgreSQL 9.5 及以上版本
+++++++++++++++++++++++++++++++++++++++

MinIO 依赖 PostgreSQL 9.5 引入的特性。

MinIO ``mc`` 命令行工具
+++++++++++++++++++++++++++++++++++++++

此过程中的某些操作需要使用 :mc:`mc` 命令行工具。
安装说明参见 ``mc`` :ref:`快速入门 <mc-install>`。

1) 向 MinIO 添加 PostgreSQL 端点
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

你可以使用环境变量 *或* 运行时配置设置来配置新的 PostgreSQL 服务端点。

.. tab-set::

   .. tab-item:: 环境变量

      MinIO 支持使用
      :ref:`环境变量
      <minio-server-envvar-bucket-notification-postgresql>` 指定 PostgreSQL 服务端点及其相关
      配置设置。:mc:`minio server` 进程会在下次启动时应用这些设置。

      下面的示例代码设置了与配置 PostgreSQL 服务端点相关的 *全部* 环境变量。
      最低 *必需* 的变量如下：

      - :envvar:`MINIO_NOTIFY_POSTGRES_CONNECTION_STRING`
      - :envvar:`MINIO_NOTIFY_POSTGRES_TABLE`
      - :envvar:`MINIO_NOTIFY_POSTGRES_FORMAT`

      .. cond:: windows

         .. code-block:: shell
            :class: copyable

               set MINIO_NOTIFY_POSTGRES_ENABLE_<IDENTIFIER>="on"
               set MINIO_NOTIFY_POSTGRES_CONNECTION_STRING_<IDENTIFIER>="host=postgresql-endpoint.example.net port=4222"
               set MINIO_NOTIFY_POSTGRES_TABLE_<IDENTIFIER>="minioevents"
               set MINIO_NOTIFY_POSTGRES_FORMAT_<IDENTIFIER>="namespace|access"
               set MINIO_NOTIFY_POSTGRES_MAX_OPEN_CONNECTIONS_<IDENTIFIER>="2"
               set MINIO_NOTIFY_POSTGRES_QUEUE_DIR_<IDENTIFIER>="/opt/minio/events"
               set MINIO_NOTIFY_POSTGRES_QUEUE_LIMIT_<IDENTIFIER>="100000"
               set MINIO_NOTIFY_POSTGRES_COMMENT_<IDENTIFIER>="PostgreSQL Notification Event Logging for MinIO"

      .. cond:: not windows

         .. code-block:: shell
            :class: copyable

               export MINIO_NOTIFY_POSTGRES_ENABLE_<IDENTIFIER>="on"
               export MINIO_NOTIFY_POSTGRES_CONNECTION_STRING_<IDENTIFIER>="host=postgresql-endpoint.example.net port=4222"
               export MINIO_NOTIFY_POSTGRES_TABLE_<IDENTIFIER>="minioevents"
               export MINIO_NOTIFY_POSTGRES_FORMAT_<IDENTIFIER>="namespace|access"
               export MINIO_NOTIFY_POSTGRES_MAX_OPEN_CONNECTIONS_<IDENTIFIER>="2"
               export MINIO_NOTIFY_POSTGRES_QUEUE_DIR_<IDENTIFIER>="/opt/minio/events"
               export MINIO_NOTIFY_POSTGRES_QUEUE_LIMIT_<IDENTIFIER>="100000"
               export MINIO_NOTIFY_POSTGRES_COMMENT_<IDENTIFIER>="PostgreSQL Notification Event Logging for MinIO"

      - 将 ``<IDENTIFIER>`` 替换为 PostgreSQL 服务端点的唯一描述性字符串。
        对于与新目标服务端点相关的所有环境变量，请使用相同的 ``<IDENTIFIER>`` 值。
        以下示例假定标识符为 ``PRIMARY``。

        如果指定的 ``<IDENTIFIER>`` 与 MinIO 部署上现有的 PostgreSQL 服务
        端点匹配，则新设置会 *覆盖* 该端点的现有设置。使用
        :mc-cmd:`mc admin config get notify_postgres <mc admin config get>` 查看
        MinIO 部署当前配置的 PostgreSQL 端点。

      - 将 ``<ENDPOINT>`` 替换为 PostgreSQL 服务端点的
        |postgresql-uri-reference|。MinIO 支持连接字符串使用 ``key=value`` 格式。
        例如：

        ``"host=https://postgresql.example.com port=5432 ..."`` 

        有关受支持的 PostgreSQL 连接字符串参数的完整文档，请参见
        |postgresql-uri-reference|。

      各环境变量的完整文档请参见 :ref:`用于存储桶通知的 PostgreSQL 服务
      <minio-server-envvar-bucket-notification-postgresql>`。

   .. tab-item:: 配置设置

      MinIO 支持在正在运行的 :mc:`minio server` 进程上，使用
      :mc-cmd:`mc admin config set` 命令和 :mc-conf:`notify_postgres` 配置键
      来新增或更新 PostgreSQL 端点。你必须重启 :mc:`minio server` 进程，
      才能应用任何新增或更新的配置设置。

      下面的示例代码设置了与配置 PostgreSQL 服务端点相关的 *全部* 设置。
      最低 *必需* 的设置如下：

      - :mc-conf:`notify_postgres connection_string
        <notify_postgres.connection_string>`
      - :mc-conf:`notify_postgres table <notify_postgres.table>`
      - :mc-conf:`notify_postgres format <notify_postgres.format>`

      .. code-block:: shell
         :class: copyable

         mc admin config set ALIAS/ notify_postgres:IDENTIFIER \
            connection_string="ENDPOINT" \
            table="<string>" \
            format="<string>" \
            max_open_connections="<string>" \
            queue_dir="<string>" \
            queue_limit="<string>" \
            comment="<string>"

      - 将 ``IDENTIFIER`` 替换为 PostgreSQL 服务端点的唯一描述性字符串。
        本过程后续示例假定标识符为 ``PRIMARY``。

        如果指定的 ``IDENTIFIER`` 与 MinIO 部署上现有的 PostgreSQL 服务端点
        匹配，则新设置会 *覆盖* 该端点的现有设置。使用
        :mc-cmd:`mc admin config get notify_postgres <mc admin config get>` 查看
        MinIO 部署当前配置的 PostgreSQL 端点。

      - 将 ``<ENDPOINT>`` 替换为 PostgreSQL 服务端点的
        `PostgreSQL URI 连接字符串
        <https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-CONNSTRING>`__。
        MinIO 支持 PostgreSQL 连接字符串使用 ``key=value`` 格式。例如：

        ``"host=https://postgresql.example.com port=5432 ..."`` 

        有关受支持的 PostgreSQL 连接字符串参数的完整文档，请参见
        |postgresql-uri-reference|。

      各设置的完整文档请参见 :ref:`PostgreSQL 存储桶通知配置设置
      <minio-server-config-bucket-notification-postgresql>`。

1) 重启 MinIO 部署
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

你必须重启 MinIO 部署才能应用配置更改。
使用 :mc-cmd:`mc admin service restart` 命令重启该部署。

.. code-block:: shell
   :class: copyable

   mc admin service restart ALIAS

将 ``ALIAS`` 替换为要重启的部署的 :ref:`别名 <alias>`。

:mc:`minio server` 进程在启动时会为每个已配置的 PostgreSQL 目标输出一行内容，
类似如下：

.. code-block:: shell

   SQS ARNs: arn:minio:sqs::primary:postgresql

当将关联的 PostgreSQL 部署作为目标配置存储桶通知时，你必须指定该 ARN 资源。

.. include:: /includes/common-bucket-notifications.rst
   :start-after: start-bucket-notification-find-arn
   :end-before: end-bucket-notification-find-arn

3) 将 PostgreSQL 端点作为目标配置存储桶通知
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc:`mc event add` 命令新增一个以已配置 PostgreSQL 服务为目标的
存储桶通知事件：

.. code-block:: shell
   :class: copyable

   mc event add ALIAS/BUCKET arn:minio:sqs::primary:postgresql \
     --event EVENTS

- 将 ``ALIAS`` 替换为 MinIO 部署的 :ref:`别名 <alias>`。
- 将 ``BUCKET`` 替换为要配置该事件的存储桶名称。
- 将 ``EVENTS`` 替换为以逗号分隔的 :ref:`事件
  <mc-event-supported-events>` 列表，MinIO 将在这些事件发生时触发通知。

使用 :mc:`mc event ls` 查看给定通知目标当前配置的所有存储桶事件：

.. code-block:: shell
   :class: copyable

   mc event ls ALIAS/BUCKET arn:minio:sqs::primary:postgresql

4) 验证已配置的事件
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

对你为其配置了新事件的存储桶执行一个操作，并检查 PostgreSQL 服务中的通知数据。
所需执行的操作取决于配置存储桶通知时指定了哪些
:mc-cmd:`事件 <mc event add --event>`。

例如，如果存储桶通知配置包含 ``s3:ObjectCreated:Put`` 事件，则可以使用
:mc:`mc cp` 命令在存储桶中新建对象并触发通知。

.. code-block:: shell
   :class: copyable

   mc cp ~/data/new-object.txt ALIAS/BUCKET

在 MinIO 部署中更新 PostgreSQL 端点
-----------------------------------

以下过程将更新 MinIO 部署中现有的 PostgreSQL 服务端点，以支持
:ref:`存储桶通知 <minio-bucket-notifications>`。

前提条件
~~~~~~~~

PostgreSQL 9.5 及以上版本
+++++++++++++++++++++++++++++++++++++++

MinIO 依赖 PostgreSQL 9.5 引入的特性。

MinIO ``mc`` 命令行工具
+++++++++++++++++++++++++++++++++++++++

此过程中的某些操作需要使用 :mc:`mc` 命令行工具。
安装说明参见 ``mc`` :ref:`快速入门 <mc-install>`。


1) 列出部署中已配置的 PostgreSQL 端点
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc admin config get` 命令列出部署中当前已配置的 PostgreSQL
服务端点：

.. code-block:: shell
   :class: copyable

   mc admin config get ALIAS/ notify_postgres

将 ``ALIAS`` 替换为 MinIO 部署的 :ref:`别名 <alias>`。

命令输出类似如下：

.. code-block:: shell

   notify_postgres:primary queue_dir="" connection_string="postgresql://" queue_limit="0"  table="" format="namespace"
   notify_postgres:secondary queue_dir="" connection_string="" queue_limit="0"  table="" format="namespace"

:mc-conf:`notify_postgres` 键是
:ref:`minio-server-config-bucket-notification-postgresql` 的顶层配置键。
:mc-conf:`connection_string <notify_postgres.connection_string>` 键为给定的
``notify_postgres`` 键指定 PostgreSQL 服务端点。
``notify_postgres:<IDENTIFIER>`` 后缀描述该 PostgreSQL 服务端点的唯一标识符。

记下你要更新的 PostgreSQL 服务端点标识符，以便下一步使用。

2) 更新 PostgreSQL 端点
~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc admin config set` 命令设置 PostgreSQL 服务端点的新配置：

.. code-block:: shell
   :class: copyable

   mc admin config set ALIAS/ notify_postgres:IDENTIFIER \
      connection_string="ENDPOINT" \
      table="<string>" \
      format="<string>" \
      max_open_connections="<string>" \
      queue_dir="<string>" \
      queue_limit="<string>" \
      comment="<string>"

以下配置设置是 PostgreSQL 服务端点的 *最低* 必需项：

- :mc-conf:`notify_postgres connection_string
  <notify_postgres.connection_string>`
- :mc-conf:`notify_postgres table <notify_postgres.table>`
- :mc-conf:`notify_postgres format <notify_postgres.format>`

其他所有配置设置均为 *可选*。
完整的 PostgreSQL 配置设置列表请参见
:ref:`minio-server-config-bucket-notification-postgresql`。

3) 重启 MinIO 部署
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

你必须重启 MinIO 部署才能应用配置更改。
使用 :mc-cmd:`mc admin service restart` 命令重启该部署。

.. code-block:: shell
   :class: copyable

   mc admin service restart ALIAS

将 ``ALIAS`` 替换为要重启的部署的 :ref:`别名 <alias>`。

:mc:`minio server` 进程在启动时会为每个已配置的 PostgreSQL 目标输出一行内容，
类似如下：

.. code-block:: shell

   SQS ARNs: arn:minio:sqs::primary:postgresql

4) 验证变更
~~~~~~~~~~~

对某个使用已更新 PostgreSQL 服务端点进行事件配置的存储桶执行操作，并检查
PostgreSQL 服务中的通知数据。所需执行的操作取决于配置存储桶通知时指定了哪些
:mc-cmd:`事件 <mc event add --event>`。

例如，如果存储桶通知配置包含 ``s3:ObjectCreated:Put`` 事件，则可以使用
:mc:`mc cp` 命令在存储桶中新建对象并触发通知。

.. code-block:: shell
   :class: copyable

   mc cp ~/data/new-object.txt ALIAS/BUCKET
