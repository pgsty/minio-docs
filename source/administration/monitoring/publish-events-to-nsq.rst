.. _minio-bucket-notifications-publish-nsq:

======================
将事件发布到 NSQ
======================

.. default-domain:: minio

.. |ARN| replace:: ``arn:minio:sqs::primary:nsq``

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 1

MinIO 支持将 :ref:`存储桶通知
<minio-bucket-notifications>` 事件发布到 `NSQ <https://nsq.io/>`__
服务端点。

为 MinIO 部署添加 NSQ 端点
--------------------------

以下过程会在 MinIO 部署中新增一个 NSQ 服务端点，用于支持
:ref:`存储桶通知 <minio-bucket-notifications>`。

前提条件
~~~~~~~~

MinIO ``mc`` 命令行工具
+++++++++++++++++++++++

此过程中的部分操作需要使用 :mc:`mc` 命令行工具。
安装说明请参见 ``mc`` :ref:`快速入门 <mc-install>`。

1) 将 NSQ 端点添加到 MinIO
~~~~~~~~~~~~~~~~~~~~~~~~~~

你可以使用环境变量，*或* 通过设置运行时配置项来配置新的 NSQ 服务端点。

.. tab-set::

   .. tab-item:: 环境变量

      MinIO 支持使用
      :ref:`环境变量
      <minio-server-envvar-bucket-notification-nsq>` 指定 NSQ 服务端点及其相关
      配置设置。:mc:`minio server` 进程会在下一次启动时应用这些指定的设置。
      
      以下示例代码设置了与配置 NSQ 服务端点相关的 *全部* 环境变量。
      *必需* 的最小变量集为
      :envvar:`MINIO_NOTIFY_NSQ_NSQD_ADDRESS` 和
      :envvar:`MINIO_NOTIFY_NSQ_TOPIC`：

      .. cond:: windows
      
         .. code-block:: shell
            :class: copyable
         
               set MINIO_NOTIFY_NSQ_ENABLE_<IDENTIFIER>="on"
               set MINIO_NOTIFY_NSQ_NSQD_ADDRESS_<IDENTIFIER>="<ENDPOINT>"
               set MINIO_NOTIFY_NSQ_TOPIC_<IDENTIFIER>="<string>"
               set MINIO_NOTIFY_NSQ_TLS_<IDENTIFIER>="<string>"
               set MINIO_NOTIFY_NSQ_TLS_SKIP_VERIFY_<IDENTIFIER>="<string>"
               set MINIO_NOTIFY_NSQ_QUEUE_DIR_<IDENTIFIER>="<string>"
               set MINIO_NOTIFY_NSQ_QUEUE_LIMIT_<IDENTIFIER>="<string>"
               set MINIO_NOTIFY_NSQ_COMMENT_<IDENTIFIER>="<string>"

      .. cond:: not windows

         .. code-block:: shell
            :class: copyable

               export MINIO_NOTIFY_NSQ_ENABLE_<IDENTIFIER>="on"
               export MINIO_NOTIFY_NSQ_NSQD_ADDRESS_<IDENTIFIER>="<ENDPOINT>"
               export MINIO_NOTIFY_NSQ_TOPIC_<IDENTIFIER>="<string>"
               export MINIO_NOTIFY_NSQ_TLS_<IDENTIFIER>="<string>"
               export MINIO_NOTIFY_NSQ_TLS_SKIP_VERIFY_<IDENTIFIER>="<string>"
               export MINIO_NOTIFY_NSQ_QUEUE_DIR_<IDENTIFIER>="<string>"
               export MINIO_NOTIFY_NSQ_QUEUE_LIMIT_<IDENTIFIER>="<string>"
               export MINIO_NOTIFY_NSQ_COMMENT_<IDENTIFIER>="<string>"

      - 将 ``<IDENTIFIER>`` 替换为目标服务端点的唯一描述性字符串。
        对与新目标服务端点相关的所有环境变量使用相同的 ``<IDENTIFIER>`` 值。
        以下示例假设该标识符为 ``PRIMARY``。

        如果指定的 ``<IDENTIFIER>`` 与 MinIO 部署上现有的 NSQ 服务
        端点匹配，则新设置会 *覆盖* 该端点的任何现有设置。使用
        :mc-cmd:`mc admin config get notify_nsq <mc admin config get>` 查看
        MinIO 部署上当前已配置的 NSQ 端点。

      - 将 ``<ENDPOINT>`` 替换为 NSQ 服务端点的 URL。
        例如，``https://nsq-service.example.com:4150``。

      有关每个环境变量的完整文档，请参见 :ref:`用于存储桶通知的 NSQ 服务
      <minio-server-envvar-bucket-notification-nsq>`。

   .. tab-item:: 配置设置

      MinIO 支持在运行中的 :mc:`minio server` 进程上使用
      :mc-cmd:`mc admin config set` 命令和 :mc-conf:`notify_nsq`
      配置键来添加或更新 NSQ 端点。你必须重启 :mc:`minio server`
      进程，才能应用任何新增或更新的配置设置。

      以下示例代码设置了与配置 NSQ 服务端点相关的 *全部* 设置。
      *必需* 的最小设置为
      :mc-conf:`notify_nsq nsqd_address <notify_nsq.nsqd_address>` 和
      :mc-conf:`notify_nsq topic <notify_nsq.topic>`：

      .. code-block:: shell
         :class: copyable

         mc admin config set ALIAS/ notify_nsq:IDENTIFIER \
           nsqd_address="ENDPOINT" \
           topic="<string>" \
           tls="<string>" \
           tls_skip_verify="<string>" \
           queue_dir="<string>" \
           queue_limit="<string>" \
           comment="<string>"


      - 将 ``IDENTIFIER`` 替换为 NSQ 服务端点的唯一描述性字符串。
        本过程中的以下示例假设该标识符为 ``PRIMARY``。

        如果指定的 ``IDENTIFIER`` 与 MinIO 部署上现有的 NSQ 服务
        端点匹配，则新设置会 *覆盖* 该端点的任何现有设置。使用
        :mc-cmd:`mc admin config get notify_nsq <mc admin config get>` 查看
        MinIO 部署上当前已配置的 NSQ 端点。

      - 将 ``ENDPOINT`` 替换为 NSQ 服务端点的 URL。
        例如：

        ``NSQ://user:password@hostname:port``

      有关每个设置的完整文档，请参见 :ref:`NSQ Bucket Notification Configuration Settings
      <minio-server-config-bucket-notification-nsq>`。

1) 重启 MinIO 部署
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

你必须重启 MinIO 部署才能应用配置更改。
使用 :mc-cmd:`mc admin service restart` 命令重启该部署。

.. code-block:: shell
   :class: copyable

   mc admin service restart ALIAS

将 ``ALIAS`` 替换为要重启部署的 :ref:`别名 <alias>`。

:mc:`minio server` 进程在启动时会为每个已配置的 NSQ
目标打印一行内容，类似如下：

.. code-block:: shell

   SQS ARNs: |ARN|

在将关联的 NSQ 部署配置为目标时，你必须在配置存储桶通知时指定 ARN 资源。

.. include:: /includes/common-bucket-notifications.rst
   :start-after: start-bucket-notification-find-arn
   :end-before: end-bucket-notification-find-arn

3) 将 NSQ 端点配置为存储桶通知目标
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc:`mc event add` 命令新增一个存储桶通知事件，并将已配置的 NSQ 服务作为目标：

.. code-block:: shell
   :class: copyable

   mc event add ALIAS/BUCKET arn:minio:sqs::primary:nsq \
     --event EVENTS

- 将 ``ALIAS`` 替换为 MinIO 部署的 :ref:`别名 <alias>`。
- 将 ``BUCKET`` 替换为要配置该事件的存储桶名称。
- 将 ``EVENTS`` 替换为逗号分隔的 :ref:`事件
  <mc-event-supported-events>` 列表，MinIO 会针对这些事件触发通知。

使用 :mc:`mc event ls` 查看给定通知目标已配置的所有存储桶事件：

.. code-block:: shell
   :class: copyable

   mc event ls ALIAS/BUCKET arn:minio:sqs::primary:nsq

4) 验证已配置的事件
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

对你配置了新事件的存储桶执行某个操作，并检查 NSQ 服务中的通知数据。
所需操作取决于在配置存储桶通知时指定了哪些 :mc-cmd:`事件 <mc event add --event>`。

例如，如果存储桶通知配置包含 ``s3:ObjectCreated:Put`` 事件，
则可以使用 :mc:`mc cp` 命令在存储桶中创建一个新对象并触发通知。

.. code-block:: shell
   :class: copyable

   mc cp ~/data/new-object.txt ALIAS/BUCKET

更新 MinIO 部署中的 NSQ 端点
----------------------------

以下过程会更新 MinIO 部署中现有的 NSQ 服务端点，以支持
:ref:`存储桶通知 <minio-bucket-notifications>`。

前提条件
~~~~~~~~~~

MinIO ``mc`` 命令行工具
+++++++++++++++++++++++

此过程中的部分操作需要使用 :mc:`mc` 命令行工具。
安装说明请参见 ``mc`` :ref:`快速入门 <mc-install>`。


1) 列出部署中已配置的 NSQ 端点
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc admin config get` 命令列出部署中当前已配置的 NSQ 服务端点：

.. code-block:: shell
   :class: copyable

   mc admin config get ALIAS/ notify_nsq

将 ``ALIAS`` 替换为 MinIO 部署的 :ref:`别名 <alias>`。

命令输出类似如下：

.. code-block:: shell

   notify_nsq:primary nsqd_address="https://nsq.example.com" queue_dir="" queue_limit="0"  tls="off" tls_skip_verify="off" topic=""
   notify_nsq:secondary nsqd_address="https://nsq.example.com" queue_dir="" queue_limit="0"  tls="off" tls_skip_verify="off" topic=""

:mc-conf:`notify_nsq` 键是 :ref:`minio-server-config-bucket-notification-nsq`
的顶层配置键。
:mc-conf:`nsqd_address <notify_nsq.nsqd_address>` 键为给定的 `notify_nsq`
键指定 NSQ 服务端点。
``notify_nsq:<IDENTIFIER>`` 后缀描述该 NSQ 服务端点的唯一标识符。

记下你要更新的 NSQ 服务端点标识符，以便在下一步使用。

2) 更新 NSQ 端点
~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc admin config set` 命令为 NSQ 服务端点设置新配置：

.. code-block:: shell
   :class: copyable

   mc admin config set ALIAS/ notify_nsq:<IDENTIFIER> \
      nsqd_address="NSQ://user:password@hostname:port" \
      topic="<string>" \
      tls="<string>" \
      tls_skip_verify="<string>" \
      queue_dir="<string>" \
      queue_limit="<string>" \
      comment="<string>"

:mc-conf:`notify_nsq nsqd_address <notify_nsq.nsqd_address>` 配置设置是 NSQ 服务端点的
*最低* 必需项。
所有其他配置设置均为 *可选*。
有关 NSQ 配置设置的完整列表，请参见 :ref:`minio-server-config-bucket-notification-nsq`。

3) 重启 MinIO 部署
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

你必须重启 MinIO 部署才能应用配置更改。
使用 :mc-cmd:`mc admin service restart` 命令重启该部署。

.. code-block:: shell
   :class: copyable

   mc admin service restart ALIAS

将 ``ALIAS`` 替换为要重启部署的 :ref:`alias <alias>`。

:mc:`minio server` 进程在启动时会为每个已配置的 NSQ
目标打印一行内容，类似如下：

.. code-block:: shell

   SQS ARNs: arn:minio:sqs::primary:NSQ

4) 验证更改
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

对使用更新后 NSQ 服务端点进行事件配置的存储桶执行某个操作，
并检查 NSQ 服务中的通知数据。
所需操作取决于在配置存储桶通知时指定了哪些 :mc-cmd:`事件 <mc event add --event>`。

例如，如果存储桶通知配置包含 ``s3:ObjectCreated:Put`` 事件，
则可以使用 :mc:`mc cp` 命令在存储桶中创建一个新对象并触发通知。

.. code-block:: shell
   :class: copyable

   mc cp ~/data/new-object.txt ALIAS/BUCKET
