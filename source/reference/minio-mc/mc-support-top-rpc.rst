=======================
``mc support top rpc``
=======================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc support top rpc

.. include:: /includes/common-mc-support.rst
   :start-after: start-minio-only
   :end-before: end-minio-only

语法
------

.. start-mc-support-top-rpc-desc

:mc:`mc support top rpc` 命令显示远程过程调用（RPC）的指标。

.. end-mc-support-top-rpc-desc

.. tab-set::

   .. tab-item:: 示例

      以下命令显示 :term:`alias` ``myminio`` 部署当前的远程过程调用指标。

      .. code-block:: shell
         :class: copyable

         mc support top rpc myminio/

      输出会返回如下信息：server、连接数、ping 时延、距离上次 ping（``pong``）的时间、重连次数、string in、string out、messages in 和 messages out。

      输出类似如下：

      .. code-block:: bash

         λ mc support top rpc myminio
               SERVER            CONCTD  PING     PONG   OUT.Q   RECONNS STR.IN  STR.OUT MSG.IN  MSG.OUT
          To  127.0.0.1:9002       5     0.7ms   1s ago    0        0     ->0      0->    3269    3212
         From 127.0.0.1:9002       5     1.1ms   1s ago    0        0     ->0      0->    3213    3269
          To  127.0.0.1:9003       5     0.6ms   1s ago    0        0     ->0      0->    6001    6076
         From 127.0.0.1:9003       5     0.6ms   1s ago    0        0     ->0      0->    6077    6001
          To  127.0.0.1:9004       5     0.6ms   1s ago    0        0     ->0      0->    3243    3160
         From 127.0.0.1:9004       5     0.4ms   1s ago    0        0     ->0      0->    3161    3243
          To  127.0.0.1:9005       5     0.6ms   1s ago    0        0     ->0      0->    3150    3094
         From 127.0.0.1:9005       5     0.3ms   1s ago    0        0     ->0      0->    3095    3150
          To  127.0.0.1:9006       5     0.3ms   1s ago    0        0     ->0      0->    3185    3221
         From 127.0.0.1:9006       5     0.6ms   1s ago    0        0     ->0      0->    3222    3185

   .. tab-item:: 语法

      该命令具有以下语法：

      .. code-block:: shell 
         :class: copyable

         mc [GLOBALFLAGS] support top rpc                 \
                                      [--airgap]          \
                                      [--in value]        \
                                      [--interval value]  \
                                      [-n value]          \
                                      [--nodes value]     \
                                      TARGET

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~~~~~~~

.. mc-cmd:: TARGET
   :required:

   命令应运行的 :ref:`alias <minio-mc-alias>` 或 :term:`prefix` 的完整路径。

.. mc-cmd:: --airgap
   :optional:

   用于无法通过网络访问 SUBNET 的环境。

.. mc-cmd:: --in
   :optional:

   回放先前保存的 JSON 文件。
   指定要回放的 JSON 文件路径，例如由此前运行该命令生成的文件。

.. mc-cmd:: --interval
   :optional:

   指标请求之间的间隔时间（秒）。

   默认情况下，命令每秒请求一次指标。

.. mc-cmd:: -n
   :optional:

   在退出前要运行的请求次数。
   使用 ``0`` 表示无限运行。

   如果未指定，命令不会自动退出。

.. mc-cmd:: --nodes
   :optional:

   以逗号分隔的节点列表，用于指定从哪些节点收集指标。


全局标志
~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals
