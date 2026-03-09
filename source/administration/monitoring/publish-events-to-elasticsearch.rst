.. _minio-bucket-notifications-publish-elasticsearch:

==========================
将事件发布到 Elasticsearch
==========================

.. default-domain:: minio

.. |ARN| replace:: ``arn:minio:sqs::primary:elasticsearch``

.. contents:: 目录
   :local:
   :depth: 1

MinIO 支持将 :ref:`bucket notification
<minio-bucket-notifications>` 事件发布到
`Elasticsearch <https://www.elastic.co/>`__ 服务端点。

MinIO 依赖 :github:`elastic/go-elasticsearch` v7 项目连接 Elastic。

为 MinIO 部署添加 Elasticsearch 端点
------------------------------------

以下步骤为 MinIO 部署添加一个新的 Elasticsearch 服务端点，以支持
:ref:`bucket notifications <minio-bucket-notifications>`。

前提条件
~~~~~~~~

Elasticsearch v7.0 及更高版本
+++++++++++++++++++++++++++++++++++++++

MinIO 依赖 :github:`olivere/elastic` v7 项目连接 Elastic。``elastic/v7``
库专门面向 Elasticsearch v7.0，与更早版本的 Elasticsearch 不兼容。

MinIO ``mc`` 命令行工具
+++++++++++++++++++++++++++++++++++++++

本步骤中的部分操作需要使用 :mc:`mc` 命令行工具。
安装说明请参见 ``mc`` :ref:`Quickstart <mc-install>`。

1) 将 Elasticsearch 端点添加到 MinIO
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

你可以使用环境变量，*或* 通过设置运行时配置项来配置新的 Elasticsearch 服务端点。

.. tab-set::

   .. tab-item:: 环境变量

      MinIO 支持使用
      :ref:`environment variables
      <minio-server-envvar-bucket-notification-elasticsearch>` 指定
      Elasticsearch 服务端点及其相关配置。:mc:`minio server` 进程会在下次
      启动时应用这些设置。
      
      以下示例代码设置了配置 Elasticsearch 服务端点所需的 *全部* 环境变量。
      最少 *必须* 设置的变量包括：

      - :envvar:`MINIO_NOTIFY_ELASTICSEARCH_ENABLE`
      - :envvar:`MINIO_NOTIFY_ELASTICSEARCH_URL`
      - :envvar:`MINIO_NOTIFY_ELASTICSEARCH_INDEX`
      - :envvar:`MINIO_NOTIFY_ELASTICSEARCH_FORMAT`

      .. cond:: windows
      
         .. code-block:: shell
            :class: copyable
         
               set MINIO_NOTIFY_ELASTICSEARCH_ENABLE_<IDENTIFIER>="on"
               set MINIO_NOTIFY_ELASTICSEARCH_URL_<IDENTIFIER>="<ENDPOINT>"
               set MINIO_NOTIFY_ELASTICSEARCH_INDEX_<IDENTIFIER>="<string>"
               set MINIO_NOTIFY_ELASTICSEARCH_FORMAT_<IDENTIFIER>="<string>"
               set MINIO_NOTIFY_ELASTICSEARCH_USERNAME_<IDENTIFIER>="<string>"
               set MINIO_NOTIFY_ELASTICSEARCH_PASSWORD_<IDENTIFIER>="<string>"
               set MINIO_NOTIFY_ELASTICSEARCH_QUEUE_DIR_<IDENTIFIER>="<string>"
               set MINIO_NOTIFY_ELASTICSEARCH_QUEUE_LIMIT_<IDENTIFIER>="<string>"
               set MINIO_NOTIFY_ELASTICSEARCH_COMMENT_<IDENTIFIER>="<string>"

      .. cond:: not windows

         .. code-block:: shell
            :class: copyable

               export MINIO_NOTIFY_ELASTICSEARCH_ENABLE_<IDENTIFIER>="on"
               export MINIO_NOTIFY_ELASTICSEARCH_URL_<IDENTIFIER>="<ENDPOINT>"
               export MINIO_NOTIFY_ELASTICSEARCH_INDEX_<IDENTIFIER>="<string>"
               export MINIO_NOTIFY_ELASTICSEARCH_FORMAT_<IDENTIFIER>="<string>"
               export MINIO_NOTIFY_ELASTICSEARCH_USERNAME_<IDENTIFIER>="<string>"
               export MINIO_NOTIFY_ELASTICSEARCH_PASSWORD_<IDENTIFIER>="<string>"
               export MINIO_NOTIFY_ELASTICSEARCH_QUEUE_DIR_<IDENTIFIER>="<string>"
               export MINIO_NOTIFY_ELASTICSEARCH_QUEUE_LIMIT_<IDENTIFIER>="<string>"
               export MINIO_NOTIFY_ELASTICSEARCH_COMMENT_<IDENTIFIER>="<string>"

      - 将 ``<IDENTIFIER>`` 替换为目标服务端点的唯一描述性字符串。
        与新目标服务端点相关的所有环境变量都应使用相同的 ``<IDENTIFIER>`` 值。
        以下示例假定该标识符为 ``PRIMARY``。

        如果指定的 ``<IDENTIFIER>`` 与 MinIO 部署中现有的 Elasticsearch
        服务端点匹配，则新设置会 *覆盖* 该端点的现有设置。使用
        :mc-cmd:`mc admin config get notify_elasticsearch <mc admin config get>`
        查看 MinIO 部署中当前已配置的 Elasticsearch 端点。

      - 将 ``<ENDPOINT>`` 替换为 Elasticsearch 服务端点的 URL。
        例如：

      有关各环境变量的完整说明，请参见 :ref:`Elasticsearch Service for Bucket Notifications
      <minio-server-envvar-bucket-notification-elasticsearch>`。

   .. tab-item:: 配置项

      MinIO 支持在运行中的 :mc:`minio server` 进程上，使用
      :mc-cmd:`mc admin config set` 命令和 :mc-conf:`notify_elasticsearch`
      配置键来添加或更新 Elasticsearch 端点。你必须重启
      :mc:`minio server` 进程，才能应用新增或更新后的配置项。

      以下示例代码设置了配置 Elasticsearch 服务端点相关的 *全部* 配置项。
      最少 *必须* 设置的配置项包括：
      
      - :mc-conf:`url <notify_elasticsearch.url>`
      - :mc-conf:`index <notify_elasticsearch.index>`
      - :mc-conf:`format <notify_elasticsearch.format>` 

      .. code-block:: shell
         :class: copyable

         mc admin config set ALIAS/ notify_elasticsearch:IDENTIFIER \
            url="ENDPOINT" \
            index="<string>" \
            format="<string>" \
            username="<string>" \
            password="<string>" \
            queue_dir="<string>" \
            queue_limit="<string>" \
            comment="<string>"


      - 将 ``IDENTIFIER`` 替换为 Elasticsearch 服务端点的唯一描述性字符串。
        本步骤中的后续示例假定该标识符为 ``PRIMARY``。

        如果指定的 ``IDENTIFIER`` 与 MinIO 部署中现有的 Elasticsearch 服务
        端点匹配，则新设置会 *覆盖* 该端点的现有设置。使用
        :mc-cmd:`mc admin config get notify_elasticsearch <mc admin config get>`
        查看 MinIO 部署中当前已配置的 Elasticsearch 端点。

      - 将 ``ENDPOINT`` 替换为 Elasticsearch 服务端点的 URL。
        例如：

        ``https://user:password@hostname:port``

      有关各配置项的完整说明，请参见 :ref:`Elasticsearch Bucket Notification Configuration Settings
      <minio-server-config-bucket-notification-elasticsearch>`。

1) 重启 MinIO 部署
~~~~~~~~~~~~~~~~~~

你必须重启 MinIO 部署以应用配置变更。
使用 :mc-cmd:`mc admin service restart` 命令重启部署。

.. code-block:: shell
   :class: copyable

   mc admin service restart ALIAS

将 ``ALIAS`` 替换为需要重启的部署 :ref:`alias <alias>`。

:mc:`minio server` 进程会在启动时为每个已配置的 Elasticsearch 目标输出一行，
类似如下：

.. code-block:: shell

   SQS ARNs: arn:minio:sqs::primary:elasticsearch

在将关联的 Elasticsearch 部署配置为存储桶通知目标时，你必须指定该 ARN 资源。

.. include:: /includes/common-bucket-notifications.rst
   :start-after: start-bucket-notification-find-arn
   :end-before: end-bucket-notification-find-arn


3) 将 Elasticsearch 端点配置为存储桶通知目标
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc:`mc event add` 命令新增存储桶通知事件，并将已配置的 Elasticsearch
服务作为目标：

.. code-block:: shell
   :class: copyable

   mc event add ALIAS/BUCKET arn:minio:sqs::primary:elasticsearch \
     --event EVENTS

- 将 ``ALIAS`` 替换为 MinIO 部署的 :ref:`alias <alias>`。
- 将 ``BUCKET`` 替换为要配置该事件的存储桶名称。
- 将 ``EVENTS`` 替换为一个以逗号分隔的 :ref:`events
  <mc-event-supported-events>` 列表，MinIO 会针对这些事件触发通知。

使用 :mc:`mc event ls` 查看给定通知目标已配置的所有存储桶事件：

.. code-block:: shell
   :class: copyable

   mc event ls ALIAS/BUCKET arn:minio:sqs::primary:elasticsearch

4) 验证已配置的事件
~~~~~~~~~~~~~~~~~~~

对已配置新事件的存储桶执行某项操作，然后检查 Elasticsearch 服务中的通知数据。
所需执行的操作取决于配置存储桶通知时指定了哪些
:mc-cmd:`events <mc event add --event>`。

例如，如果存储桶通知配置包含 ``s3:ObjectCreated:Put`` 事件，则可以使用
:mc:`mc cp` 命令在存储桶中创建新对象并触发通知。

.. code-block:: shell
   :class: copyable

   mc cp ~/data/new-object.txt ALIAS/BUCKET

更新 MinIO 部署中的 Elasticsearch 端点
--------------------------------------

以下步骤更新 MinIO 部署中现有的 Elasticsearch 服务端点，以支持
:ref:`bucket notifications <minio-bucket-notifications>`。

前提条件
~~~~~~~~

Elasticsearch v7.0 及更高版本
+++++++++++++++++++++++++++++++++++++++

MinIO 依赖 :github:`olivere/elastic` v7 项目连接 Elastic。``elastic/v7``
库专门面向 Elasticsearch v7.0，与更早版本的 Elasticsearch 不兼容。

MinIO ``mc`` 命令行工具
+++++++++++++++++++++++++++++++++++++++

本步骤中的部分操作需要使用 :mc:`mc` 命令行工具。
安装说明请参见 ``mc`` :ref:`Quickstart <mc-install>`。


1) 列出部署中已配置的 Elasticsearch 端点
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc admin config get` 命令列出部署中当前已配置的
Elasticsearch 服务端点：

.. code-block:: shell
   :class: copyable

   mc admin config get ALIAS/ notify_elasticsearch

将 ``ALIAS`` 替换为 MinIO 部署的 :ref:`alias <alias>`。

命令输出类似如下：

.. code-block:: shell

   notify_elasticsearch:primary  queue_dir="" queue_limit="0"  url="https://user:password@hostname:port" format="namespace" index=""
   notify_elasticsearch:secondary queue_dir="" queue_limit="0"  url="https://user:password@hostname:port" format="namespace" index=""

:mc-conf:`notify_elasticsearch` 键是
:ref:`minio-server-config-bucket-notification-elasticsearch` 的顶层配置键。
:mc-conf:`url <notify_elasticsearch.url>` 键指定给定
`notify_elasticsearch` 键对应的 Elasticsearch 服务端点。
``notify_elasticsearch:<IDENTIFIER>`` 后缀表示该 Elasticsearch 服务端点的
唯一标识符。

记下你要更新的 Elasticsearch 服务端点标识符，以便下一步使用。

2) 更新 Elasticsearch 端点
~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc admin config set` 命令为 Elasticsearch 服务端点设置新配置：

.. code-block:: shell
   :class: copyable

   mc admin config set ALIAS/ notify_elasticsearch:<IDENTIFIER> \
      url="https://user:password@hostname:port" \
      index="<string>" \
      format="<string>" \
      username="<string>" \
      password="<string>" \
      queue_dir="<string>" \
      queue_limit="<string>" \
      comment="<string>"

:mc-conf:`notify_elasticsearch url <notify_elasticsearch.url>` 配置项是
Elasticsearch 服务端点 *最少* 必须设置的项。其他所有配置项均为 *可选*。
完整的 Elasticsearch 配置项列表，请参见
:ref:`minio-server-config-bucket-notification-elasticsearch`。

3) 重启 MinIO 部署
~~~~~~~~~~~~~~~~~~

你必须重启 MinIO 部署以应用配置变更。
使用 :mc-cmd:`mc admin service restart` 命令重启部署。

.. code-block:: shell
   :class: copyable

   mc admin service restart ALIAS

将 ``ALIAS`` 替换为需要重启的部署 :ref:`alias <alias>`。

:mc:`minio server` 进程会在启动时为每个已配置的 Elasticsearch 目标输出一行，
类似如下：

.. code-block:: shell

   SQS ARNs: arn:minio:sqs::primary:elasticsearch

4) 验证变更
~~~~~~~~~~~

对某个已使用更新后 Elasticsearch 服务端点配置事件的存储桶执行操作，然后检查
Elasticsearch 服务中的通知数据。所需执行的操作取决于配置存储桶通知时指定了哪些
:mc-cmd:`events <mc event add --event>`。

例如，如果存储桶通知配置包含 ``s3:ObjectCreated:Put`` 事件，则可以使用
:mc:`mc cp` 命令在存储桶中创建新对象并触发通知。

.. code-block:: shell
   :class: copyable

   mc cp ~/data/new-object.txt ALIAS/BUCKET
