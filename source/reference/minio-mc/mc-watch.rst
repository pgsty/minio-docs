============
``mc watch``
============

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc watch

语法
----

.. start-mc-watch-desc

:mc:`mc watch` 命令用于监视指定 MinIO 存储桶或本地文件系统路径上的事件。
对于 S3 服务，请使用 :mc:`mc event add` 在兼容 S3 的服务上配置存储桶事件通知。

.. end-mc-watch-desc

你也可以将 :mc:`mc watch` 用于本地文件系统目录，
以获得与运行 ``inotify -e modify,create,delete,move``
命令类似的结果。

.. tab-set::

   .. tab-item:: 示例

      以下命令会监视 ``myminio`` MinIO 部署中 ``mydata`` 存储桶内任意对象或前缀上的
      :ref:`事件 <mc-event-supported-events>`：

      .. code-block:: shell
         :class: copyable

         mc watch --recursive myminio/mydata

   .. tab-item:: 语法

      该命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] watch                \
                          [--event "string"]   \
                          [--prefix "string"]  \
                          [--recursive]        \
                          [--suffix "string"]  \
                          ALIAS

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~

.. mc-cmd:: ALIAS

   *必需* MinIO 部署的 :ref:`alias <alias>` 以及要监视已配置事件的存储桶完整路径。
   例如：

   .. code-block:: shell

      mc watch myminio/mybucket

.. mc-cmd:: --event

   要监视的事件。使用英文逗号 ``,`` 分隔可指定多个事件。
   支持的事件见 :ref:`mc-event-supported-events`。

   默认值为 ``put,delete, get``。

.. mc-cmd:: --prefix

   在该存储桶前缀下监视 :mc-cmd:`~mc watch --event` 指定的事件。

   例如，若 :mc-cmd:`~mc watch ALIAS` 为 ``play/mybucket``，且
   :mc-cmd:`~mc watch --prefix` 为 ``photos``，则仅 ``play/mybucket/photos``
   中的事件会触发存储桶通知。

.. mc-cmd:: --recursive, r

   在指定的 :mc-cmd:`~mc watch ALIAS` 存储桶路径或本地目录中递归监视事件。

.. mc-cmd:: --suffix

   在该存储桶后缀下监视 :mc-cmd:`~mc watch --event` 指定的事件。

   例如，若 :mc-cmd:`~mc watch ALIAS` 为 ``play/mybucket``，且
   :mc-cmd:`~mc watch --suffix` 为 ``.jpg``，则仅 ``play/mybucket/*.jpg``
   中的事件会触发存储桶通知。

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-json-globals
   :end-before: end-minio-mc-json-globals

示例
----

监视存储桶中的事件
~~~~~~~~~~~~~~~~~~

.. code-block:: shell
   :class: copyable

   mc watch --recursive ALIAS/PATH

- 将 :mc-cmd:`ALIAS <mc watch ALIAS>` 替换为 S3 兼容主机的 :mc:`alias <mc alias>`。

- 将 :mc-cmd:`PATH <mc watch ALIAS>` 替换为存储桶路径。


行为
----

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
