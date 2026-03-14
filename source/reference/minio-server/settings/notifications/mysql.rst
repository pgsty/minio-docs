.. _minio-server-envvar-bucket-notification-mysql:
.. _minio-server-config-bucket-notification-mysql:

======================
MySQL 通知设置
======================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

本页记录了将 MySQL 服务配置为 :ref:`存储桶通知 <minio-bucket-notifications>` 目标的相关设置。
有关如何使用这些设置的教程，请参阅 :ref:`minio-bucket-notifications-publish-mysql`。

.. include:: /includes/common-mc-admin-config.rst
   :start-after: start-minio-settings-defined
   :end-before: end-minio-settings-defined

.. include:: /includes/common-mc-admin-config.rst
   :start-after: start-minio-settings-test-before-prod
   :end-before: end-minio-settings-test-before-prod

多个 MySQL 目标
----------------

你可以在顶层键后追加唯一标识符 ``_ID``，为每组相关的 MySQL 设置指定多个 MySQL 服务端点。

示例
~~~~

以下命令分别将两个不同的 MySQL 服务端点设置为 ``PRIMARY`` 和 ``SECONDARY``：

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. code-block:: shell
         :class: copyable
      
         export MINIO_NOTIFY_MYSQL_ENABLE_PRIMARY="on"
         export MINIO_NOTIFY_MYSQL_DSN_STRING_PRIMARY="username:password@tcp(mysql.example.com:3306)/miniodb"
         export MINIO_NOTIFY_MYSQL_TABLE_PRIMARY="minioevents"
         export MINIO_NOTIFY_MYSQL_FORMAT_PRIMARY="namespace"
      
         export MINIO_NOTIFY_MYSQL_ENABLE_SECONDARY="on"
         export MINIO_NOTIFY_MYSQL_DSN_STRING_SECONDARY="username:password@tcp(mysql.example.com:3306)/miniodb"
         export MINIO_NOTIFY_MYSQL_TABLE_SECONDARY="minioevents"
         export MINIO_NOTIFY_MYSQL_FORMAT_SECONDARY="namespace"

      在这些设置中，:envvar:`MINIO_NOTIFY_MYSQL_ENABLE_PRIMARY <MINIO_NOTIFY_MYSQL_ENABLE>` 表示该环境变量关联到 ID 为 ``PRIMARY`` 的 MySQL 服务端点。

   .. tab-item:: 配置设置
      :sync: config

      .. code-block:: shell

         mc admin config set notify_mysql:primary \ 
            dsn_string="username:password@tcp(mysql.example.com:3306)/miniodb"
            table="minioevents" \
            format="namespace" \
            [ARGUMENT=VALUE ...]
   
         mc admin config set notify_mysql:secondary \
            dsn_string="username:password@tcp(mysql.example.com:3306)/miniodb"
            table="minioevents" \
            format="namespace" \
            [ARGUMENT=VALUE ...]

设置
----

启用
~~~~

*必需*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_MYSQL_ENABLE

      指定 ``on`` 以启用将存储桶通知发布到 MySQL 服务端点。

      默认为 ``off``。
   
      若设置为 ``on``，则还必须指定以下环境变量：
   
      - :envvar:`MINIO_NOTIFY_MYSQL_DSN_STRING`
      - :envvar:`MINIO_NOTIFY_MYSQL_TABLE`
      - :envvar:`MINIO_NOTIFY_MYSQL_FORMAT`

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: notify_mysql
   
      用于定义 MySQL 服务端点以配合 :ref:`MinIO bucket notifications <minio-bucket-notifications>` 使用的顶层配置键。
   
      使用 :mc-cmd:`mc admin config set` 设置或更新 MySQL 服务端点。
      每个目标都*必需*以下参数：
      
      - :mc-conf:`~notify_mysql.dsn_string`
      - :mc-conf:`~notify_mysql.table`
      - :mc-conf:`~notify_mysql.format`
   
      其他可选参数请以空白字符（``" "``）分隔列表形式指定。

      .. code-block:: shell
         :class: copyable
   
         mc admin config set notify_mysql \ 
           dsn_string="username:password@tcp(mysql.example.com:3306)/miniodb"
           table="minioevents" \
           format="namespace" \
           [ARGUMENT="VALUE"] ... \


数据源名称（DSN）字符串
~~~~~~~~~~~~~~~~~~~~~~~~

*必需*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar
      
      .. envvar:: MINIO_NOTIFY_MYSQL_DSN_STRING

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: notify_mysql dsn_string
         :delimiter: " "

指定 MySQL 服务端点的数据源名称（DSN）。MinIO 期望使用以下格式：

``<user>:<password>@tcp(<host>:<port>)/<database>``
 
例如：
 
``"username:password@tcp(mysql.example.com:3306)/miniodb"``
      
.. include:: /includes/linux/minio-server.rst
   :start-after: start-notify-target-online-desc
   :end-before: end-notify-target-online-desc

表
~~

*必需*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_MYSQL_TABLE

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: notify_mysql table
         :delimiter: " "

指定 MinIO 发布事件通知到的 MySQL 表名。
      
格式
~~~~

*必需*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_MYSQL_FORMAT

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: notify_mysql format
         :delimiter: " "
      
指定写入 MySQL 服务端点的事件数据格式。
MinIO 支持以下取值：

``namespace``
   对于每个存储桶事件，MinIO 会创建一个 JSON 文档，将事件中的存储桶和对象名称作为文档 ID，并将实际事件作为文档体的一部分。
   对该对象的后续更新会修改该对象在表中的现有条目。
   同样，删除该对象也会删除对应的表条目。
   
``access``
   对于每个存储桶事件，MinIO 会创建一个包含事件详情的 JSON 文档，并以 MySQL 生成的随机 ID 追加到表中。
   对对象的后续更新会产生新的索引条目，且现有条目保持不变。
      
最大打开连接数
~~~~~~~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_MYSQL_MAX_OPEN_CONNECTIONS

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: notify_mysql max_open_connections
         :delimiter: " "

指定与 MySQL 数据库建立的最大打开连接数。

默认为 ``2``。
      
队列目录
~~~~~~~~

*可选*


.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_MYSQL_QUEUE_DIR

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: notify_mysql queue_dir
         :delimiter: " "

指定目录路径，以启用 MinIO 对未投递消息的持久化事件存储，例如 ``/opt/minio/events``。

当 MySQL 服务器/代理离线时，MinIO 会将未投递事件存储在指定位置；连接恢复后会重放这些已存储事件。

队列上限
~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_MYSQL_QUEUE_LIMIT

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: notify_mysql queue_limit
         :delimiter: " "

指定未投递消息的最大上限。默认为 ``100000``。

注释
~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_MYSQL_COMMENT

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: notify_mysql comment
         :delimiter: " "

指定与 MySQL 配置关联的注释。
