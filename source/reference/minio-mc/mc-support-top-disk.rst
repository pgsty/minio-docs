=======================
``mc support top disk``
=======================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc support top disk

.. include:: /includes/common-mc-support.rst
   :start-after: start-minio-only
   :end-before: end-minio-only

语法
----

.. start-mc-support-top-disk-desc

:mc:`mc support top disk` 命令显示当前驱动器统计信息。

.. end-mc-support-top-disk-desc

.. tab-set::

   .. tab-item:: 示例

      以下命令显示 :term:`alias` ``myminio`` 上当前正在进行的 S3 API 调用。

      .. code-block:: shell
         :class: copyable

         mc support top disk myminio/

   .. tab-item:: 语法

      该命令具有以下语法：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] support top disk                     \
                                      [--count, -c "integer"]  \
                                      TARGET

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~

.. mc-cmd:: TARGET
   :required:

   要运行该命令的 :ref:`alias <minio-mc-alias>` 或 :term:`prefix` 的完整路径。

.. mc-cmd:: --count, -c
   :optional:

   显示统计信息，最多显示到指定数量的驱动器。

   如果未提供值，该命令最多返回 10 个驱动器的统计信息。

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals
