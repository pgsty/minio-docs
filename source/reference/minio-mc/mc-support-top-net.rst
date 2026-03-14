=======================
``mc support top net``
=======================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc support top net

.. include:: /includes/common-mc-support.rst
   :start-after: start-minio-only
   :end-before: end-minio-only

语法
----

.. start-mc-support-top-net-desc

:mc:`mc support top net` 命令用于显示实时网络指标。

.. end-mc-support-top-net-desc

.. tab-set::

   .. tab-item:: 示例

      以下命令显示 :term:`alias` ``myminio`` 部署当前的实时网络指标。

      .. code-block:: shell
         :class: copyable

         mc support top net myminio/

      输出将返回服务器 URL、网络接口、接收速率、发送速率和系统消息等信息。

   .. tab-item:: 语法

      该命令的语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] support top disk                \
                                      [--interval value]  \
                                      TARGET

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~

.. mc-cmd:: TARGET
   :required:

   命令应运行的 :ref:`alias <minio-mc-alias>` 或 :term:`prefix` 的完整路径。

.. mc-cmd:: --interval
   :optional:

   两次指标请求之间的间隔（秒）。

   默认情况下，命令每秒请求一次指标。

全局参数
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals
