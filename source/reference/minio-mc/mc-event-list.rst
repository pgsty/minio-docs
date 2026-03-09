.. _minio-mc-event-list:

===============
``mc event ls``
===============

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc event list
.. mc:: mc event ls

语法
----

.. start-mc-event-list-desc

:mc:`mc event ls` 命令列出某个存储桶的所有事件通知触发器。

.. end-mc-event-list-desc

别名 :mc:`mc event list` 与 :mc:`mc event ls` 功能等效。

.. tab-set::

   .. tab-item:: 示例

      以下命令列出 ``myminio`` MinIO 部署中 ``mydata`` 存储桶、指定
      :ref:`bucket notification target <minio-bucket-notifications>` 的所有已配置事件通知：

      .. code-block:: shell
         :class: copyable

         mc event ls myminio myminio/mydata arn:aws:sqs::primary:target

   .. tab-item:: 语法

      该命令的语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS]

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


.. code-block:: shell

   mc [GLOBALFLAGS] event ls [FLAGS] ALIAS ARN

参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   命令要列出事件通知的 S3 服务 :ref:`alias <alias>` 和存储桶。
   例如：

   .. code-block:: shell

      mc event ls play/mybucket ARN...

.. mc-cmd:: ARN
   :required:

   存储桶资源的 :aws-docs:`Amazon Resource Name (ARN) <IAM/latest/UserGuide/reference-arns>`。

   MinIO 服务器在启动时会为每个已配置的通知目标输出一个 ARN。
   更多信息请参见 :ref:`Bucket Notifications <minio-bucket-notifications>`。


全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

列出存储桶上的事件通知
~~~~~~~~~~~~~~~~~~~~

.. tab-set::

   .. tab-item:: 示例

      以下命令列出某个存储桶上的所有事件通知触发器。

      .. code-block:: shell
         :class: copyable

         mc event ls myminio/mydata

   .. tab-item:: 语法

      .. code-block:: shell
         :class: copyable

         mc event ls ALIAS ARN

      - 将 ``ALIAS`` 替换为要添加存储桶通知事件的 MinIO 部署
        :ref:`alias <alias>`。例如：

        ``myminio/mydata``

      - 将 ``ARN`` 替换为通知目标
        :mc-cmd:`ARN <mc event add ARN>`。

行为
----

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
