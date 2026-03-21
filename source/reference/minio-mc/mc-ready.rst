============
``mc ready``
============

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc ready

语法
------

.. start-mc-ready-desc

:mc:`mc ready` 命令用于检查集群状态，以及集群是否具有 ``read`` 和 ``write`` quorum。

.. end-mc-ready-desc

.. tab-set::

   .. tab-item:: 示例

      以下命令向别名为 ``myminio`` 的集群发送 ``GET`` 请求，并返回其状态。

      .. code-block:: shell
         :class: copyable

         mc ready myminio

      该命令会向 :mc:`~mc alias` ``myminio`` 对应的部署发送 ``GET`` 请求。
      该命令会重复发送请求，直到成功为止。

      在别名 ``myminio`` 对应的集群就绪之前，输出类似如下：

      .. code-block:: text

         The cluster `myminio` is unreachable: Get "http://myminio.example.com:9000/minio/health/cluster": dial tcp 198.51.100.0:9000: connect: connection refused

      当请求成功连接到 ``myminio`` 部署后，输出类似如下：
     
      .. code-block:: text

         The cluster `myminio` is ready

   .. tab-item:: 语法

      该命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] ready            \
                          TARGET           \
                          [--cluster-read] \
                          [--maintenance]

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~~~~~~~

.. mc-cmd:: TARGET
   :required:

   命令运行目标的完整路径，可为 :ref:`alias <minio-mc-alias>` 或前缀。

.. mc-cmd:: --cluster-read
   :optional:

   检查集群是否具有足够的 :term:`quorum <read quorum>` 来处理 ``READ`` 请求。

.. mc-cmd:: --maintenance
   :optional:
   
   检查当该别名对应节点因维护下线时，集群是否仍可维持 read 和 write quorum。

   对预期下线维护的具体节点使用其 alias，而不要使用指向负载均衡器的 alias。

全局标志
~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
--------

检查集群是否具有 read quorum
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下命令检查某个部署是否有足够可用驱动器来执行读取操作。

.. code-block:: shell
   :class: copyable

   mc read myminio --cluster-read

检查集群是否处于维护下线场景
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下命令检查当别名 ``myminio`` 对应节点下线时，集群在维护期间是否仍可维持 read 和 write quorum。

.. code-block:: shell
   :class: copyable

   mc ready myminio --maintenance
