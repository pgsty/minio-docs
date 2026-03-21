.. _minio-mc-event-add:

================
``mc event add``
================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc event add

语法
----

.. start-mc-event-add-desc

:mc:`mc event add` 命令为存储桶添加事件通知触发器。

.. end-mc-event-add-desc

MinIO 会将已触发的事件自动发送到已配置的
:ref:`notification target <minio-bucket-notifications>`。

.. tab-set::

   .. tab-item:: EXAMPLE

      以下命令为 ``myminio`` MinIO 部署中的 ``mydata`` 存储桶上的
      所有 ``PUT`` 和 ``DELETE`` 操作创建新的事件通知触发器：

      .. code-block:: shell
         :class: copyable

         mc event add --event "put,delete" myminio/mydata arn:aws:sqs::primary:target

      指定的 ARN 对应 ``myminio`` 部署上已配置的
      :ref:`bucket notification target <minio-bucket-notifications>`。

   .. tab-item:: SYNTAX

      命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] event add \
                          [--event "string"]  \
                          [--ignore-existing] \
                          [--prefix "string"] \
                          [--suffix "string"] \
                          ALIAS               \
                          ARN


      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   要添加新事件通知的 MinIO :ref:`alias <alias>` 和存储桶。
   例如：

   .. code-block:: shell

      mc event add play/mybucket

.. mc-cmd:: ARN
   :required:

   通知目标的 :aws-docs:`Amazon Resource Name (ARN) <IAM/latest/UserGuide/reference-arns>`。

   MinIO 服务器启动时会为每个已配置的通知目标输出一个 ARN。
   更多信息请参见 :ref:`存储桶通知 <minio-bucket-notifications>`。

.. mc-cmd:: --event
   :optional:

   MinIO 生成存储桶通知所依据的事件。

   支持以下取值：

   - ``put``
   - ``get``
   - ``delete``

   使用逗号 ``,`` 分隔可指定多个值。
   值之间不要添加空白字符。

   如果未指定，默认值为 ``put,delete,get``。

   各支持值对应的 S3 事件详见 :ref:`mc-event-supported-events`。

.. mc-cmd:: ignore-existing, p
   :optional:

   如果已存在匹配的触发器，则指示 MinIO 忽略指定的事件触发器。

.. mc-cmd:: --prefix
   :optional:

   指定可由 :mc-cmd:`~mc event add --event` 触发存储桶通知的存储桶前缀。

   例如，若 :mc-cmd:`~mc event add ALIAS` 为 ``play/mybucket`` 且 :mc-cmd:`~mc event add --prefix` 为 ``photos``，则只有 ``play/mybucket/photos`` 中的事件会触发存储桶通知。

   省略该参数时，存储桶中所有前缀和对象的事件都可触发通知。

.. mc-cmd:: --suffix
   :optional:

   指定可由 :mc-cmd:`~mc event add --event` 触发存储桶通知的存储桶后缀。

   例如，若 :mc-cmd:`~mc event add ALIAS` 为 ``play/mybucket`` 且 :mc-cmd:`~mc event add --suffix` 为 ``.jpg``，则只有 ``play/mybucket/*.jpg`` 中的事件会触发存储桶通知。

   省略该参数时，无论后缀为何，所有对象的事件都可触发通知。

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

为存储桶添加事件通知
~~~~~~~~~~~~~~~~~~

.. tab-set::

   .. tab-item:: Example

      以下命令为某个存储桶上的所有 S3 ``PUT``、``GET`` 和 ``DELETE``
      操作添加新的事件通知触发器。该命令假设 MinIO 部署中至少已配置一个
      :ref:`bucket notification target <minio-bucket-notifications>`：

      .. code-block:: shell
         :class: copyable

         mc event add myminio/mydata arn:minio:sqs::primary:webhook

   .. tab-item:: Syntax

      .. code-block:: shell
         :class: copyable

         mc event add ALIAS ARN

      - 将 ``ALIAS`` 替换为 MinIO 部署的 :ref:`alias <alias>` 和要添加
        存储桶通知事件的存储桶。例如：

        ``myminio/mydata``

      - 将 ``ARN`` 替换为通知目标
        :mc-cmd:`ARN <mc event add ARN>`。


行为
----

.. _mc-event-supported-events:

支持的存储桶事件
~~~~~~~~~~~~~~~~

下表列出了 :mc:`mc event add` 支持的取值及其对应的
:ref:`S3 events <minio-bucket-notifications-event-types>`：

.. list-table::
   :header-rows: 1
   :widths: 30 70
   :width: 100%

   * - Supported Value
     - 对应的 S3 事件

   * - ``put``
     - | :data:`s3:ObjectCreated:CompleteMultipartUpload`
       | :data:`s3:ObjectCreated:Copy`
       | :data:`s3:ObjectCreated:DeleteTagging`
       | :data:`s3:ObjectCreated:Post`
       | :data:`s3:ObjectCreated:Put`
       | :data:`s3:ObjectCreated:PutLegalHold`
       | :data:`s3:ObjectCreated:PutRetention`
       | :data:`s3:ObjectCreated:PutTagging`

   * - ``get``
     - | :data:`s3:ObjectAccessed:Head`
       | :data:`s3:ObjectAccessed:Get`
       | :data:`s3:ObjectAccessed:GetRetention`
       | :data:`s3:ObjectAccessed:GetLegalHold`

   * - ``delete``
     - | :data:`s3:ObjectRemoved:Delete`
       | :data:`s3:ObjectRemoved:DeleteMarkerCreated`

   * - ``replica``
     - | :data:`s3:Replication:OperationCompletedReplication`
       | :data:`s3:Replication:OperationFailedReplication`
       | :data:`s3:Replication:OperationMissedThreshold`
       | :data:`s3:Replication:OperationNotTracked`
       | :data:`s3:Replication:OperationReplicatedAfterThreshold`

   * - ``ilm``
     - | :data:`s3:ObjectTransition:Failed`
       | :data:`s3:ObjectTransition:Complete`
       | :data:`s3:ObjectRestore:Post`
       | :data:`s3:ObjectRestore:Completed`

   * - ``scanner``
     - | :data:`s3:Scanner:ManyVersions`
       | :data:`s3:Scanner:BigPrefix`

有关上述 S3 事件的更完整文档，请参见 :s3-docs:`S3 Supported Event Types <NotificationHowTo.html#notification-how-to-event-types-and-destinations>`。

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
