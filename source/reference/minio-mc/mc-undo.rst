.. _minio-mc-undo:

===========
``mc undo``
===========

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc undo

语法
----

.. start-mc-undo-desc

:mc:`mc undo` 命令用于撤销指定路径上由 ``PUT`` 或 ``DELETE`` 操作引起的更改。

.. end-mc-undo-desc

.. tab-set::

   .. tab-item:: 示例

      以下命令会回滚 ``myminio`` 部署中 ``data`` 存储桶内 ``file.zip`` 对象最近三次上传和/或删除操作：

      .. code-block:: shell
         :class: copyable

         mc undo myminio/data/file.zip --last 3

   .. tab-item:: 语法

      命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] undo                \
                          TARGET              \
                          [--action "type"]   \
                          [--force]           \
                          [--last "integer"]  \
                          [--recursive, r]    \
                          [--dry-run]

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


参数
~~~~

.. mc-cmd:: TARGET
   :required:

   命令应执行的对象或前缀的完整路径。
   路径必须包含 :ref:`ALIAS <minio-mc-alias>`、存储桶以及前缀或对象名称。

.. mc-cmd:: --action
   :optional:

   撤销指定类型的最近一次更改。
   可接受的值为 ``DELETE`` 或 ``PUT``。

   默认情况下，:mc:`mc undo` 会同时回滚 ``DELETE`` 和 ``PUT`` 操作。
   使用 :mc-cmd:`~mc undo --action` 可在两者中选择其一，但仅针对该类型最近的一次操作。

   以下命令会回滚 ``data`` 存储桶中对象 ``today.zip`` 最近一次 ``PUT``，恢复到该对象的上一版本：

   .. code-block:: shell
      :class: copyable

      mc undo myminio/data/today.zip --action "PUT"

   以下示例会回滚前缀 ``archive`` 最近一次 ``DELETE``，并递归恢复该前缀及其所有子对象：

   .. code-block:: shell
      :class: copyable

      mc undo myminio/data/archive --recursive --action "DELETE"

   与 :mc-cmd:`~mc undo --last` 互斥。

.. mc-cmd:: --dry-run
   :optional:

   输出命令结果，但不实际执行操作。
   使用此标志可测试按特定方式运行命令时的结果。

.. mc-cmd:: --force
   :optional:

   强制执行递归操作。

.. mc-cmd:: --last
   :optional:

   接受一个整数值，用于指定要撤销的 ``PUT`` 和/或 ``DELETE`` 更改次数。

   若未指定，命令默认回滚一次（``1``）操作。
   与 :mc-cmd:`~mc undo --action` 互斥。

.. mc-cmd:: --recursive, r
   :optional:

   以递归方式执行命令。
   例如，可使用此标志撤销某个前缀上的更改。


全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

撤销对象最近三次上传或删除
~~~~~~~~~~~~~~~~~~~~~~~~~~

以下命令会回滚 ``myminio`` 部署中 ``data`` 存储桶内 ``file.zip`` 对象最近三次上传和/或删除操作：

.. code-block:: shell
   :class: copyable

   mc undo myminio/data/file.zip --last 3

撤销某个前缀下任意对象最近一次上传或删除
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc:`mc undo` 回滚在 ``myminio`` 别名下、``data`` 存储桶中 ``presentations/recordings/`` :term:`prefix` 内最近一次 ``PUT`` 或 ``DELETE`` 操作：

.. code-block:: shell
   :class: copyable

   mc undo myminio/data/presentations/recordings/ --recursive --force

行为
----

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
