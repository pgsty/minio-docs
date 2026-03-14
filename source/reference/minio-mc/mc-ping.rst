===========
``mc ping``
===========

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc ping

语法
----

.. start-mc-ping-desc

:mc:`mc ping` 命令对指定目标执行存活性检查。

.. end-mc-ping-desc

.. tab-set::

   .. tab-item:: 示例

      以下命令向目标发送请求，并输出响应的最小、最大、平均与往返时间，以及处理请求时遇到的错误数量。

      .. code-block:: shell
         :class: copyable

         mc ping play --count 5

      该命令对 :mc:`~mc alias` ``play`` 对应的部署执行五次 ping。
      输出类似如下：

      .. code-block:: shell

         1: https://play.min.io   min=213.00ms   max=213.00ms   average=213.00ms   errors=0   roundtrip=213.00ms
         2: https://play.min.io   min=67.15ms    max=213.00ms   average=140.07ms   errors=0   roundtrip=67.15ms 
         3: https://play.min.io   min=67.15ms    max=213.00ms   average=115.85ms   errors=0   roundtrip=67.41ms 
         4: https://play.min.io   min=61.26ms    max=213.00ms   average=102.20ms   errors=0   roundtrip=61.26ms 
         5: https://play.min.io   min=61.26ms    max=213.00ms   average=95.03ms    errors=0   roundtrip=66.36ms 

   .. tab-item:: 语法

      该命令的语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] ping                       \
                          TARGET                     \
                          [--count, -c value]        \
                          [--error-count, -e value]  \
                          [--interval, -i value]     \
                          [--distributed, -a value]

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~

.. mc-cmd:: TARGET
   :required:

   命令应运行所在位置的 :ref:`alias <minio-mc-alias>` 或前缀完整路径。

.. mc-cmd:: --count
   :optional:

   指定执行检查的次数。

   如果未指定，存活性检查会持续执行，直到手动停止。

.. mc-cmd:: --error-count
   :optional:
   
   指定在退出前允许出现的错误次数。

   例如，要在出现五次错误后停止 ping 进程，请使用：

   .. code-block:: shell
      :class: copyable

      mc ping TARGET -e 5

.. mc-cmd:: --exit
   :optional:

   .. versionadded:: RELEASE.2023-05-30T22-41-38Z
      
   在首次检查成功后退出。

.. mc-cmd:: --interval
   :optional:

   指定请求之间的等待时间（秒）。

   默认情况下，命令在两次请求之间等待 1 秒。

.. mc-cmd:: --distributed
   :optional:

   向 MinIO 集群中的所有服务器发送请求。
         
   .. note::
      
      在可直接访问每个节点或 Pod 的分布式部署中使用此选项。
      当节点位于服务（例如负载均衡器）之后时，此标志不起作用。

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

返回 5 次请求的延迟与存活性
~~~~~~~~~~~~~~~~~~~~~~~~~~

以下命令对别名为 ``myminio`` 的部署发送五次存活性检查，输出每次检查结果后结束。

.. code-block:: shell
   :class: copyable

   mc ping myminio --count 5

每次请求间隔 5 分钟持续发送存活性检查
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下命令持续发送存活性检查请求，且每次请求之间间隔 5 分钟（300 秒）。

.. code-block:: shell
   :class: copyable

   mc ping myminio --interval 300

错误次数达到 20 后结束存活性检查
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下命令持续发送存活性检查，直到累计出现 20 次错误：

.. code-block:: shell
   :class: copyable

   mc ping myminio --error-count 20
