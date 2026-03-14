.. _minio-bucket-notifications-publish-mysql:

===================
将事件发布到 MySQL
===================

.. default-domain:: minio

.. |ARN| replace:: ``arn:minio:sqs::primary:mysql``

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 1

MinIO 支持将 :ref:`存储桶通知
<minio-bucket-notifications>` 事件发布到 `MySQL <https://www.mysql.com/>`__
服务端点。MinIO *仅* 支持 MySQL 5.7.8 及更高版本。

向 MinIO 部署添加 MySQL 端点
----------------------------

以下过程会向 MinIO 部署添加一个新的 MySQL 服务端点，以支持
:ref:`存储桶通知 <minio-bucket-notifications>`。

先决条件
~~~~~~~~

MySQL 5.7.8 及更高版本
++++++++++++++++++++++

MinIO 依赖于 MySQL 5.7.8 中引入的功能。

MinIO ``mc`` 命令行工具
+++++++++++++++++++++++

此过程会在某些操作中使用 :mc:`mc` 命令行工具。
安装说明请参阅 ``mc`` :ref:`快速入门 <mc-install>`。

1) 将 MySQL 端点添加到 MinIO
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

你可以使用环境变量*或*设置运行时配置设置来配置新的 MySQL 服务端点。

.. tab-set::

   .. tab-item:: 环境变量

      MinIO 支持使用
      :ref:`环境变量
      <minio-server-envvar-bucket-notification-mysql>` 指定 MySQL 服务端点及其相关
      配置设置。:mc:`minio server` 进程会在下次启动时应用指定的设置。

      以下示例代码设置了与配置 MySQL 服务端点相关的 *所有* 环境变量。最少的
      *必需* 变量为：

      - :envvar:`MINIO_NOTIFY_MYSQL_ENABLE`
      - :envvar:`MINIO_NOTIFY_MYSQL_DSN_STRING`
      - :envvar:`MINIO_NOTIFY_MYSQL_TABLE`
      - :envvar:`MINIO_NOTIFY_MYSQL_FORMAT`

      .. cond:: windows

         .. code-block:: shell
            :class: copyable

               set MINIO_NOTIFY_MYSQL_ENABLE_<IDENTIFIER>="on"
               set MINIO_NOTIFY_MYSQL_DSN_STRING_<IDENTIFIER>="user:password@tcp(hostname:port)/database"
               set MINIO_NOTIFY_MYSQL_TABLE_<IDENTIFIER>="minio-events"
               set MINIO_NOTIFY_MYSQL_FORMAT_<IDENTIFIER>="namespace|access"
               set MINIO_NOTIFY_MYSQL_MAX_OPEN_CONNECTIONS_<IDENTIFIER>="2"
               set MINIO_NOTIFY_MYSQL_QUEUE_DIR_<IDENTIFIER>="/opt/minio/events"
               set MINIO_NOTIFY_MYSQL_QUEUE_LIMIT_<IDENTIFIER>="100000"
               set MINIO_NOTIFY_MYSQL_COMMENT_<IDENTIFIER>="MySQL Event Notification Logging for MinIO"

      .. cond:: not windows

         .. code-block:: shell
            :class: copyable

               export MINIO_NOTIFY_MYSQL_ENABLE_<IDENTIFIER>="on"
               export MINIO_NOTIFY_MYSQL_DSN_STRING_<IDENTIFIER>="user:password@tcp(hostname:port)/database"
               export MINIO_NOTIFY_MYSQL_TABLE_<IDENTIFIER>="minio-events"
               export MINIO_NOTIFY_MYSQL_FORMAT_<IDENTIFIER>="namespace|access"
               export MINIO_NOTIFY_MYSQL_MAX_OPEN_CONNECTIONS_<IDENTIFIER>="2"
               export MINIO_NOTIFY_MYSQL_QUEUE_DIR_<IDENTIFIER>="/opt/minio/events"
               export MINIO_NOTIFY_MYSQL_QUEUE_LIMIT_<IDENTIFIER>="100000"
               export MINIO_NOTIFY_MYSQL_COMMENT_<IDENTIFIER>="MySQL Event Notification Logging for MinIO"

      - 将 ``<IDENTIFIER>`` 替换为该 MySQL 服务端点的唯一描述性字符串。对于与新目标服务端点相关的所有环境变量，请使用相同的 ``<IDENTIFIER>`` 值。
        以下示例假定标识符为 ``PRIMARY``。

        如果指定的 ``<IDENTIFIER>`` 与 MinIO 部署中现有的 MySQL 服务
        端点匹配，则新设置会*覆盖*该端点的任何现有设置。使用
        :mc-cmd:`mc admin config get notify_mysql <mc admin config get>` 查看
        MinIO 部署上当前配置的 MySQL 端点。

      - 将 ``<ENDPOINT>`` 替换为 MySQL 服务端点的 DSN。
        MinIO 期望采用以下格式：

        ``<user>:<password>@tcp(<host>:<port>)/<database>``

        例如：

        ``"username:password@tcp(mysql.example.com:3306)/miniodb"``

      有关每个环境变量的完整文档，请参阅 :ref:`用于存储桶通知的 MySQL 服务
      <minio-server-envvar-bucket-notification-mysql>`。

   .. tab-item:: 配置设置

      MinIO 支持在运行中的
      :mc:`minio server` 进程上使用 :mc-cmd:`mc admin config set` 命令
      和 :mc-conf:`notify_mysql` 配置键添加或更新 MySQL 端点。你必须重启
      :mc:`minio server` 进程，才能应用任何新增或更新的配置设置。

      以下示例代码设置了与配置 MySQL 服务端点相关的 *所有* 设置。最少的
      *必需* 设置为：

      - :mc-conf:`notify_mysql dsn_string <notify_mysql.dsn_string>`
      - :mc-conf:`notify_mysql table <notify_mysql.table>`
      - :mc-conf:`notify_mysql format <notify_mysql.format>`

      .. code-block:: shell
         :class: copyable

         mc admin config set ALIAS/ notify_mysql:IDENTIFIER \
            dsn_string="<ENDPOINT>" \
            table="<string>" \
            format="<string>" \
            max_open_connections="<string>" \
            queue_dir="<string>" \
            queue_limit="<string>" \
            comment="<string>"

      - 将 ``IDENTIFIER`` 替换为该 MySQL 服务端点的唯一描述性字符串。
        本过程中的以下示例假定标识符为 ``PRIMARY``。

        如果指定的 ``IDENTIFIER`` 与 MinIO 部署中现有的 MySQL 服务
        端点匹配，则新设置会*覆盖*该端点的任何现有设置。使用
        :mc-cmd:`mc admin config get notify_mysql <mc admin config get>` 查看
        MinIO 部署上当前配置的 MySQL 端点。

      - 将 ``<ENDPOINT>`` 替换为 MySQL 服务端点的 DSN。
        MinIO 期望采用以下格式：

        ``<user>:<password>@tcp(<host>:<port>)/<database>``

        例如：

        ``"username:password@tcp(mysql.example.com:3306)/miniodb"``

      有关每项设置的完整文档，请参阅 :ref:`MySQL 存储桶通知配置设置
      <minio-server-config-bucket-notification-mysql>`。

1) 重启 MinIO 部署
~~~~~~~~~~~~~~~~~~

你必须重启 MinIO 部署才能应用配置更改。
使用 :mc-cmd:`mc admin service restart` 命令重启该部署。

.. code-block:: shell
   :class: copyable

   mc admin service restart ALIAS

将 ``ALIAS`` 替换为要重启的部署的 :ref:`别名 <alias>`。

:mc:`minio server` 进程在启动时会为每个已配置的 MySQL
目标输出一行，类似如下：

.. code-block:: shell

   SQS ARNs: arn:minio:sqs::primary:mysql

当将关联的 MySQL 部署配置为目标时，你必须在配置存储桶通知时指定该 ARN 资源。

.. include:: /includes/common-bucket-notifications.rst
   :start-after: start-bucket-notification-find-arn
   :end-before: end-bucket-notification-find-arn

3) 使用 MySQL 端点作为目标配置存储桶通知
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc:`mc event add` 命令添加新的存储桶通知事件，并将已配置的
MySQL 服务作为目标：

.. code-block:: shell
   :class: copyable

   mc event add ALIAS/BUCKET arn:minio:sqs::primary:mysql \
     --event EVENTS

- 将 ``ALIAS`` 替换为 MinIO 部署的 :ref:`别名 <alias>`。
- 将 ``BUCKET`` 替换为要在其中配置事件的存储桶名称。
- 将 ``EVENTS`` 替换为以逗号分隔的 :ref:`事件
  <mc-event-supported-events>` 列表，MinIO 会在这些事件发生时触发通知。

使用 :mc:`mc event ls` 查看给定通知目标上已配置的所有存储桶事件：

.. code-block:: shell
   :class: copyable

   mc event ls ALIAS/BUCKET arn:minio:sqs::primary:mysql

4) 验证已配置的事件
~~~~~~~~~~~~~~~~~~~

对已配置新事件的存储桶执行某个操作，并检查 MySQL 服务中的通知数据。
所需的操作取决于在配置存储桶通知时指定了哪些
:mc-cmd:`事件 <mc event add --event>`。

例如，如果存储桶通知配置包含
``s3:ObjectCreated:Put`` 事件，则可以使用
:mc:`mc cp` 命令在存储桶中创建一个新对象并触发通知。

.. code-block:: shell
   :class: copyable

   mc cp ~/data/new-object.txt ALIAS/BUCKET

更新 MinIO 部署中的 MySQL 端点
------------------------------

以下过程会更新 MinIO 部署中现有的 MySQL 服务端点，以支持
:ref:`存储桶通知 <minio-bucket-notifications>`。

先决条件
~~~~~~~~~~~~~~

MySQL 5.7.8 及更高版本
++++++++++++++++++++++

MinIO 依赖于 MySQL 5.7.8 中引入的功能。

MinIO ``mc`` 命令行工具
+++++++++++++++++++++++

此过程会在某些操作中使用 :mc:`mc` 命令行工具。
安装说明请参阅 ``mc`` :ref:`快速入门 <mc-install>`。


1) 列出部署中已配置的 MySQL 端点
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc admin config get` 命令列出部署中当前已配置的
MySQL 服务端点：

.. code-block:: shell
   :class: copyable

   mc admin config get ALIAS/ notify_mysql

将 ``ALIAS`` 替换为 MinIO 部署的 :ref:`别名 <alias>`。

命令输出类似如下：

.. code-block:: shell

   notify_mysql:primary format="namespace" table="minio_images" dsn_string="user:pass@tcp(mysql.example.com:3306)/miniodb"
   notify_mysql:secondary format="namespace" table="minio_images" dsn_string="user:pass@tcp(mysql.example.com:3306)/miniodb"

:mc-conf:`notify_mysql` 键是
:ref:`minio-server-config-bucket-notification-mysql` 的顶级配置键。
:mc-conf:`dsn_string <notify_mysql.dsn_string>` 键指定给定 `notify_mysql`
键对应的 MySQL 服务端点。``notify_mysql:<IDENTIFIER>`` 后缀描述该 MySQL
服务端点的唯一标识符。

记下你要在下一步更新的 MySQL 服务端点标识符。

2) 更新 MySQL 端点
~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc admin config set` 命令为 MySQL 服务端点设置新的配置：

.. code-block:: shell
   :class: copyable

   mc admin config set ALIAS/ notify_mysql:IDENTIFIER \
      dsn_string="<ENDPOINT>" \
      table="<string>" \
      format="<string>" \
      max_open_connections="<string>" \
      queue_dir="<string>" \
      queue_limit="<string>" \
      comment="<string>"

以下配置设置是 MySQL 服务端点的*最小必需*项：

- :mc-conf:`notify_mysql dsn_string <notify_mysql.dsn_string>`
- :mc-conf:`notify_mysql table <notify_mysql.table>`
- :mc-conf:`notify_mysql format <notify_mysql.format>`

所有其他配置设置均为*可选*。有关 MySQL 配置设置的完整列表，请参阅
:ref:`minio-server-config-bucket-notification-mysql`。

3) 重启 MinIO 部署
~~~~~~~~~~~~~~~~~~

你必须重启 MinIO 部署才能应用配置更改。
使用 :mc-cmd:`mc admin service restart` 命令重启该部署。

.. code-block:: shell
   :class: copyable

   mc admin service restart ALIAS

将 ``ALIAS`` 替换为要重启的部署的 :ref:`别名 <alias>`。

:mc:`minio server` 进程在启动时会为每个已配置的 MySQL
目标输出一行，类似如下：

.. code-block:: shell

   SQS ARNs: arn:minio:sqs::primary:mysql

4) 验证更改
~~~~~~~~~~~

对一个事件配置使用了更新后 MySQL 服务端点的存储桶执行某个操作，并检查
MySQL 服务中的通知数据。所需的操作取决于在配置存储桶通知时指定了哪些
:mc-cmd:`事件 <mc event add --event>`。

例如，如果存储桶通知配置包含
``s3:ObjectCreated:Put`` 事件，则可以使用
:mc:`mc cp` 命令在存储桶中创建一个新对象并触发通知。

.. code-block:: shell
   :class: copyable

   mc cp ~/data/new-object.txt ALIAS/BUCKET
