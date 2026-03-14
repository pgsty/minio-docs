.. _minio-mc-event-remove:

===============
``mc event rm``
===============

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc event remove
.. mc:: mc event rm

语法
------

.. start-mc-event-remove-desc

:mc:`mc event rm` 命令用于从存储桶中移除事件通知触发器。

.. end-mc-event-remove-desc

:mc:`mc event remove` 命令与 :mc:`mc event rm` 功能等效。

.. tab-set::

   .. tab-item:: EXAMPLE

      以下命令会在 ``myminio`` MinIO 部署的 ``mydata`` 存储桶上，
      移除指定 :ref:`bucket notification target <minio-bucket-notifications>`
      的已配置事件通知：

      .. code-block:: shell
         :class: copyable

         mc event rm myminio/mydata arn:aws:sqs::primary:target

   .. tab-item:: SYNTAX

      命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] event remove        \
                          ALIAS               \
                          [ARN]               \
                          [--event "string"]  \
                          [--force]           \
                          [--prefix "string"] \
                          [--suffix "string"]

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


.. code-block:: shell

   mc [GLOBALFLAGS] event remove [FLAGS] ALIAS ARN

参数
~~~~~~~~~~

.. mc-cmd:: ALIAS
   :required:

   用于移除事件通知的 S3 服务 :ref:`alias <alias>` 和存储桶。
   例如：

   .. code-block:: shell

      mc event rm play/mybucket

.. mc-cmd:: ARN
   :required:

   通知目标的 :aws-docs:`Amazon Resource Name (ARN) <IAM/latest/UserGuide/reference-arns>`。

   MinIO 服务器在启动时会为每个已配置的通知目标输出一个 ARN。
   更多信息请参见 :ref:`minio-bucket-notifications`。

   可在存储桶上运行 :mc:`mc event ls` 获取 ARN。

.. mc-cmd:: --event
   :optional:

   添加事件时指定的事件类型。
   这些条目**必须**与添加事件时使用的值一致。
   如果没有事件与事件类型列表匹配，命令将返回 ``no notification configuration matched`` 错误。

   使用逗号 ``,`` 分隔可指定多个事件。
   支持的事件类型请参见 :ref:`mc-event-supported-events`。

   默认移除 :mc-cmd:`~mc event rm ALIAS` 存储桶中、与 :mc-cmd:`~mc event rm ARN` 通知目标关联且对所有事件类型触发的事件。

   可通过在存储桶上运行 :mc:`mc event ls` 获取所使用的事件类型。
   使用下表将命令输出中的事件类型转换为 :mc:`mc event rm` 命令所需的条目：

   .. list-table::
      :header-rows: 1
      :widths: 50 50
      :width: 100%
   
      * - ``mv event ls`` 的输出
        - 要使用的事件类型
   
      * - ``s3:objectAccessed``
        - ``get``
   
      * - ``s3:objectCreated``
        - ``put``
   
      * - ``s3:objectRemoved``
        - ``delete``
   
   例如，如果 ``mc event ls`` 返回如下内容：

   .. code-block:: shell

      arn:minio:sqs::mytest:webhook   s3:ObjectAccessed:*,s3:ObjectCreated:*   Filter: 
      
   使用以下命令移除该事件：

   .. code-block:: shell

      mc event rm alias/bucket arn:minio:sqs::mytest:webhook --event get,put

   事件类型的顺序无关紧要，只需包含与该事件中已有类型相同的条目即可。


.. mc-cmd:: --force
   :optional:

   移除 :mc-cmd:`~mc event rm ALIAS` 存储桶中与 :mc-cmd:`~mc event rm ARN` 通知目标关联的所有事件。

.. mc-cmd:: --prefix
   :optional:

   命令移除存储桶通知时使用的存储桶前缀。

   例如，若 :mc-cmd:`~mc event rm ALIAS` 为
   ``play/mybucket``，且 :mc-cmd:`~mc event rm --prefix` 为
   ``photos``，则命令仅移除
   ``play/mybucket/photos``.

.. mc-cmd:: --suffix
   :optional:

   命令移除存储桶通知时使用的存储桶后缀。

   例如，若 :mc-cmd:`~mc event rm ALIAS` 为
   ``play/mybucket``，且 :mc-cmd:`~mc event rm --suffix` 为
   ``.jpg``，则命令仅移除
   ``play/mybucket/*.jpg``.

全局标志
~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
--------

从存储桶中移除事件通知
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tab-set::

   .. tab-item:: 示例

      以下命令会移除某个存储桶上的所有事件通知触发器。
      该命令假设 MinIO 部署中至少配置了一个
      :ref:`bucket notification target <minio-bucket-notifications>`:

      .. code-block:: shell
         :class: copyable

         mc event rm myminio/mydata arn:minio:sqs::primary:webhook

   .. tab-item:: 语法

      .. code-block:: shell
         :class: copyable

         mc event rm ALIAS ARN

      - 将 ``ALIAS`` 替换为要在其上添加存储桶通知事件的 MinIO
        部署 :ref:`alias <alias>`。例如：

        ``myminio/mydata``

      - 将 ``ARN`` 替换为通知目标
        :mc-cmd:`ARN <mc event add ARN>`.

行为
--------

S3 兼容性
~~~~~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
