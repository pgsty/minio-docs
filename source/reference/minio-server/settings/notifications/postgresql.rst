.. _minio-server-envvar-bucket-notification-postgresql:
.. _minio-server-config-bucket-notification-postgresql:

================================
PostgreSQL 通知设置
================================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

本页记录了将 PostgreSQL 服务配置为 :ref:`存储桶通知 <minio-bucket-notifications>` 目标的设置。
有关如何使用这些设置的教程，请参阅 :ref:`minio-bucket-notifications-publish-postgresql`。

.. include:: /includes/common-mc-admin-config.rst
   :start-after: start-minio-settings-defined
   :end-before: end-minio-settings-defined

.. include:: /includes/common-mc-admin-config.rst
   :start-after: start-minio-settings-test-before-prod
   :end-before: end-minio-settings-test-before-prod

多个 PostgreSQL 目标
-------------------

你可以通过在顶层键后追加唯一标识符 ``_ID``，为每组相关 PostgreSQL 设置指定多个 PostgreSQL 服务端点。
例如，以下命令分别将两个不同的 PostgreSQL 服务端点设置为 ``PRIMARY`` 和 ``SECONDARY``：

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. code-block:: shell
         :class: copyable
      
         export MINIO_NOTIFY_POSTGRES_ENABLE_PRIMARY="on"
         export MINIO_NOTIFY_POSTGRES_CONNECTION_STRING_PRIMARY="host=postgresql-endpoint.example.net port=4222..."
         export MINIO_NOTIFY_POSTGRES_TABLE_PRIMARY="minioevents"
         export MINIO_NOTIFY_POSTGRES_FORMAT_PRIMARY="namespace"
      
         export MINIO_NOTIFY_POSTGRES_ENABLE_SECONDARY="on"
         export MINIO_NOTIFY_POSTGRES_CONNECTION_STRING_SECONDARY="host=postgresql-endpoint.example.net port=4222..."
         export MINIO_NOTIFY_POSTGRES_TABLE_SECONDARY="minioevents"
         export MINIO_NOTIFY_POSTGRES_FORMAT_SECONDARY="namespace"

   .. tab-item:: 配置设置
      :sync: config

      .. code-block:: shell
   
         mc admin config set notify_postgres:primary \ 
            connection_string="host=postgresql.example.com port=5432..."
            table="minioevents" \
            format="namespace" \
            [ARGUMENT=VALUE ...]
   
         mc admin config set notify_postgres:secondary \
            connection_string="host=postgresql.example.com port=5432..."
            table="minioevents" \
            format="namespace" \
            [ARGUMENT=VALUE ...]

在这些设置中，:envvar:`MINIO_NOTIFY_POSTGRES_ENABLE_PRIMARY <MINIO_NOTIFY_POSTGRES_ENABLE>` 表示该环境变量关联到 ID 为 ``PRIMARY`` 的 PostgreSQL 服务端点。

设置
----

启用
~~~~

*必填*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_POSTGRES_ENABLE

      指定 ``on`` 以启用将存储桶通知发布到 PostgreSQL 服务端点。

      默认为 ``off``。

      如果设置为 ``on``，则还必须指定以下环境变量：
   
      - :envvar:`MINIO_NOTIFY_POSTGRES_CONNECTION_STRING`
      - :envvar:`MINIO_NOTIFY_POSTGRES_TABLE`
      - :envvar:`MINIO_NOTIFY_POSTGRES_FORMAT`

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: notify_postgres

      用于定义 PostgreSQL 服务端点的顶层配置键，可用于 :ref:`MinIO bucket notifications <minio-bucket-notifications>`。
   
      使用 :mc-cmd:`mc admin config set` 设置或更新 PostgreSQL 服务端点。
      每个目标都*必须*提供以下参数：
      
      - :mc-conf:`~notify_postgres.connection_string`
      - :mc-conf:`~notify_postgres.table`
      - :mc-conf:`~notify_postgres.format`
   
      其他可选参数以空白字符（``" "``）分隔的列表形式指定。

      .. code-block:: shell
         :class: copyable
   
         mc admin config set notify_postgres                            \ 
           connection_string="host=postgresql.example.com port=5432..." \
           table="minioevents"                                          \
           format="namespace"                                           \
           [ARGUMENT="VALUE"] ... 

连接字符串
~~~~~~~~~~

*必填*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_POSTGRES_CONNECTION_STRING

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: notify_postgres connection_string
         :delimiter: " "

指定 PostgreSQL 服务端点的 `URI connection string <https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-CONNSTRING>`__。
MinIO 支持 ``key=value`` 格式的 PostgreSQL 连接字符串。
例如：

``"host=https://postgresql.example.com port=5432 ..."``

有关受支持 PostgreSQL 连接字符串参数的完整文档，请参阅 `PostgreSQL Connection Strings documentation <https://www.postgresql.org/docs/current/libpq-connect.html#LIBPQ-CONNSTRING>`__。

.. include:: /includes/linux/minio-server.rst
   :start-after: start-notify-target-online-desc
   :end-before: end-notify-target-online-desc

表
~~

*必填*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_POSTGRES_TABLE

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: notify_postgres table
         :delimiter: " "

指定 MinIO 发布事件通知的 PostgreSQL 表名称。

格式
~~~~

*必填*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_POSTGRES_FORMAT

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: notify_postgres format
         :delimiter: " "
      
指定写入 PostgreSQL 服务端点的事件数据格式。
MinIO 支持以下取值：

``namespace``
   对于每个存储桶事件，MinIO 会创建一个 JSON 文档，使用事件中的存储桶和对象名称作为文档 ID，并将实际事件作为文档内容的一部分。
   对该对象的后续更新会修改该对象在表中的现有条目。
   同样，删除该对象也会删除对应的表条目。
   
``access``
   对于每个存储桶事件，MinIO 会创建包含事件详情的 JSON 文档，并以 PostgreSQL 生成的随机 ID 将其追加到表中。
   对对象的后续更新会产生新的索引条目，现有条目保持不变。

最大打开连接数
~~~~~~~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_POSTGRES_MAX_OPEN_CONNECTIONS

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: notify_postgres max_open_connections
         :delimiter: " "

指定与 PostgreSQL 数据库建立的最大打开连接数。

默认为 ``2``。

队列目录
~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar
      
      .. envvar:: MINIO_NOTIFY_POSTGRES_QUEUE_DIR

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: notify_postgres queue_dir
         :delimiter: " "

指定目录路径以启用 MinIO 的持久化事件存储，用于保存未投递消息，例如 ``/opt/minio/events``。

当 PostgreSQL server/broker 离线时，MinIO 会将未投递事件存储在指定存储中，并在连接恢复后重放这些已存储事件。

队列上限
~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_POSTGRES_QUEUE_LIMIT

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: notify_postgres queue_limit
         :delimiter: " "

指定未投递消息的数量上限。
默认为 ``100000``。

注释
~~~~

*可选*

.. tab-set:: 

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_POSTGRES_COMMENT

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: notify_postgres comment
         :delimiter: " "

指定与 PostgreSQL 配置关联的注释。
