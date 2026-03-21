.. _minio-server-envvar-bucket-notification-elasticsearch:
.. _minio-server-config-bucket-notification-elasticsearch:

==============================
Elasticsearch 通知设置
==============================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

本文档介绍如何配置 Elasticsearch 服务作为 :ref:`存储桶通知 <minio-bucket-notifications>` 的目标。
有关如何使用这些设置的教程，请参阅 :ref:`minio-bucket-notifications-publish-elasticsearch`。

.. include:: /includes/common-mc-admin-config.rst
   :start-after: start-minio-settings-defined
   :end-before: end-minio-settings-defined

.. include:: /includes/common-mc-admin-config.rst
   :start-after: start-minio-settings-test-before-prod
   :end-before: end-minio-settings-test-before-prod

多个 Elasticsearch 目标
----------------------

可通过为每组相关设置追加唯一标识符 ``_ID`` 来指定多个 Elasticsearch 服务端点。
例如，以下命令分别将两个不同的 Elasticsearch 服务端点设置为 ``PRIMARY`` 和 ``SECONDARY``：

示例
~~~~

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. code-block:: shell
         :class: copyable
      
         export MINIO_NOTIFY_ELASTICSEARCH_ENABLE_PRIMARY="on"
         export MINIO_NOTIFY_ELASTICSEARCH_URL_PRIMARY="https://user:password@elasticsearch-endpoint.example.net:9200"
         export MINIO_NOTIFY_ELASTICSEARCH_INDEX_PRIMARY="bucketevents"
         export MINIO_NOTIFY_ELASTICSEARCH_FORMAT_PRIMARY="namespace"
      
         export MINIO_NOTIFY_ELASTICSEARCH_ENABLE_SECONDARY="on"
         export MINIO_NOTIFY_ELASTICSEARCH_URL_SECONDARY="https://user:password@elasticsearch-endpoint.example.net:9200"
         export MINIO_NOTIFY_ELASTICSEARCH_INDEX_SECONDARY="bucketevents"
         export MINIO_NOTIFY_ELASTICSEARCH_FORMAT_SECONDARY="namespace"

   .. tab-item:: 配置设置
      :sync: config

      .. code-block:: shell

         mc admin config set notify_elasticsearch:primary \ 
            url="user:password@https://elasticsearch-endpoint.example.net:9200" \
            index="bucketevents" \
            format="namespace" \
            [ARGUMENT=VALUE ...]

         mc admin config set notify_elasticsearch:secondary \
            url="user:password@https://elasticsearch-endpoint.example.net:9200" \
            index="bucketevents" \
            format="namespace" \
            [ARGUMENT=VALUE ...]

      注意，对于配置设置，唯一标识符只追加到 ``notify_elasticsearch``，而不是每个单独参数。

设置
----

启用
~~~~

*必需*

.. tab-set::

   .. tab-item:: 环境变量
      :selected:

      .. envvar:: MINIO_NOTIFY_ELASTICSEARCH_ENABLE

      指定 ``on`` 以启用将存储桶通知发布到 Elasticsearch 服务端点。
      
      默认为 ``off``。

      如果设置为 ``on``，则还必须指定以下环境变量：
   
      - :envvar:`MINIO_NOTIFY_ELASTICSEARCH_URL`
      - :envvar:`MINIO_NOTIFY_ELASTICSEARCH_INDEX`
      - :envvar:`MINIO_NOTIFY_ELASTICSEARCH_FORMAT`

   .. tab-item:: 配置项
      
      .. mc-conf:: notify_elasticsearch
      
         用于定义 Elasticsearch 服务端点以配合 :ref:`MinIO bucket notifications <minio-bucket-notifications>` 使用的顶级配置键。
      
         使用 :mc-cmd:`mc admin config set` 设置或更新 Elasticsearch 服务端点。
         对于每个目标，以下参数为*必需*：
         
         - :mc-conf:`~notify_elasticsearch.url`
         - :mc-conf:`~notify_elasticsearch.index`
         - :mc-conf:`~notify_elasticsearch.format`
         
         其他可选参数以空白字符（``" "``）分隔的列表形式指定。
      
         .. code-block:: shell
            :class: copyable
      
            mc admin config set notify_elasticsearch \ 
              url="https://user:password@elasticsearch.example.com:9200" \
              [ARGUMENT="VALUE"] ... \

URL
~~~

*必需*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_ELASTICSEARCH_URL

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: notify_elasticsearch url
         :delimiter: " "

指定 MinIO 将存储桶事件发布到的 Elasticsearch 服务端点。
例如：``https://elasticsearch.example.com:9200``。

MinIO 支持通过 URL 参数传递认证信息，格式为 ``PROTOCOL://USERNAME:PASSWORD@HOSTNAME:PORT``。

.. include:: /includes/linux/minio-server.rst
   :start-after: start-notify-target-online-desc
   :end-before: end-notify-target-online-desc

Index
~~~~~

*必需*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_ELASTICSEARCH_INDEX

   .. tab-item:: 配置项

      .. mc-conf:: notify_elasticsearch index
         :delimiter: " "

指定用于存储或更新 MinIO 存储桶事件的 Elasticsearch index 名称。
如果 index 不存在，Elasticsearch 会自动创建。

Format
~~~~~~

*必需*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_ELASTICSEARCH_FORMAT

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: notify_elasticsearch format
         :delimiter: " "

指定写入 Elasticsearch index 的事件数据格式。
MinIO 支持以下取值：

``namespace``
   对于每个存储桶事件，MinIO 会创建一个 JSON 文档，以事件中的存储桶名和对象名作为文档 ID，并将实际事件作为文档正文的一部分。
   对该对象的后续更新会修改该对象在 index 中的现有条目。
   同样，删除该对象也会删除对应的 index 条目。
   
``access``
   对于每个存储桶事件，MinIO 会创建一个包含事件详情的 JSON 文档，并以 Elasticsearch 生成的随机 ID 将其追加到 index。
   对对象的后续更新会产生新的 index 条目，而现有条目保持不变。

Username
~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_ELASTICSEARCH_USERNAME

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: notify_elasticsearch username
         :delimiter: " "

用于连接启用了认证的 Elasticsearch 服务端点的用户名。

Password
~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_ELASTICSEARCH_PASSWORD

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: notify_elasticsearch password
         :delimiter: " "

用于连接启用了认证的 Elasticsearch 服务端点的密码。

.. versionchanged:: RELEASE.2023-06-23T20-26-00Z

   当该值作为 :mc-cmd:`mc admin config get` 的返回内容一部分时，MinIO 会对其进行脱敏。

队列目录
~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_ELASTICSEARCH_QUEUE_DIR

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: notify_elasticsearch queue_dir 
         :delimiter: " "

指定目录路径以启用 MinIO 针对未投递消息的持久化事件存储，例如 ``/opt/minio/events``。

当 Elasticsearch 服务离线时，MinIO 会将未投递事件存储在指定存储中，并在连接恢复后重放这些已存储事件。

队列上限
~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_ELASTICSEARCH_QUEUE_LIMIT

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: notify_elasticsearch queue_limit 
         :delimiter: " "

指定未投递消息的最大上限。
默认为 ``100000``。

注释
~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_NOTIFY_ELASTICSEARCH_COMMENT

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: notify_elasticsearch comment 
         :delimiter: " "

指定与 Elasticsearch 配置关联的注释。
