.. _minio-mc-admin-rebalance:

======================
``mc admin rebalance``
======================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc admin rebalance

权限
----

执行此命令的用户必须拥有该部署的 :policy-action:`admin:Rebalance` :ref:`策略操作 <minio-policy>`。

描述
----

.. start-mc-admin-rebalance-desc

:mc-cmd:`mc admin rebalance` 命令可用于在 MinIO 部署上启动、监控或停止再平衡操作。
再平衡会在部署中的所有池之间重新分配对象。

.. end-mc-admin-rebalance-desc

当新增 服务器池 时，MinIO 不会自动对对象执行再平衡。
相反，MinIO 会将 :ref:`新对象写入 <minio-writing-files>` 相较于部署中其他可用池可用空间更多的池。
触发手动再平衡流程会促使 MinIO 扫描整个部署，并按需移动对象，以使所有池的可用空间大致相当。

这是一项开销大且耗时的操作。
建议仅在部署负载较轻或空闲时运行再平衡流程。
如果在再平衡期间发生写操作，这些写操作会并行处理，并写入当前未参与再平衡的池。

你可以停止再平衡，并在之后按需重新启动。

使用以下命令跟踪正在进行的再平衡操作进度：

.. code-block:: shell
   :class: copyable

   mc admin trace --call rebalance ALIAS

.. admonition:: 仅在 MinIO 部署上使用 ``mc admin``
   :class: note

   .. include:: /includes/facts-mc-admin.rst
      :start-after: start-minio-only
      :end-before: end-minio-only


:mc-cmd:`mc admin rebalance` 命令具有以下子命令：

.. list-table::
   :header-rows: 1
   :widths: 40 60

   * - 子命令
     - 说明

   * - :mc-cmd:`mc admin rebalance start`
     - 在 MinIO 部署上启动再平衡操作。

   * - :mc-cmd:`mc admin rebalance status`
     - 输出正在进行的再平衡操作的当前状态。

   * - :mc-cmd:`mc admin rebalance stop`
     - 停止正在进行中的再平衡操作。

语法
----

.. mc-cmd:: start
   :fullpath:

   为 MinIO 部署启动再平衡操作。

   .. tab-set::

      .. tab-item:: EXAMPLES

         假设一个 MinIO 部署有两个池，并分配了别名 ``minio1``。
         其中一个池有 250 GB 可用空间，另一个池有 3 TB 可用空间。

         :mc:`mc admin rebalance` 命令会将对象从可用空间较少的池迁移到可用空间较多的池，使两个池的可用空间大致相等。

         .. code-block:: shell
            :class: copyable

            mc admin rebalance start minio1

      .. tab-item:: SYNTAX

         该命令的语法如下：

         .. code-block:: shell
            :class: copyable

            mc [GLOBALFLAGS] admin rebalance start ALIAS

         - 将 ALIAS 替换为要执行再平衡的 MinIO 部署的 :ref:`别名 <alias>`。

.. mc-cmd:: status
   :fullpath:

   查询具有活动再平衡进程的部署，并返回该再平衡进程的状态信息。

   状态输出包括再平衡操作 ID、操作时间，以及部署中每个池的详细信息。
   对每个池，状态会显示池 ID、该池的再平衡状态、已用空间百分比以及该池的再平衡进度。

   .. tab-set::

      .. tab-item:: EXAMPLE

         .. code-block:: shell
            :class: copyable

            mc admin rebalance status minio1

      .. tab-item:: SYNTAX

         该命令的语法如下：

         .. code-block:: shell

            mc [GLOBALFLAGS] admin rebalance ALIAS
      
         - 将 ALIAS 替换为 MinIO 部署的 :ref:`别名 <alias>`。

.. mc-cmd:: stop
   :fullpath:

   结束指定部署上正在进行中的再平衡任务。

   .. tab-set::
      
      .. tab-item:: EXAMPLES
         
         .. code-block:: shell
            :class: copyable

            mc admin rebalance stop minio1
        
      .. tab-item:: SYNTAX
         
         该命令的语法如下：

         .. code-block:: shell
            
            mc [GLOBALFLAGS] admin rebalance stop ALIAS

         - 将 ALIAS 替换为 MinIO 部署的 :ref:`别名 <alias>`。

全局标志
--------

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

行为
----

先备份集群设置
~~~~~~~~~~~~~~

在开始下线操作前，使用 :mc:`mc admin cluster bucket export` 和 :mc:`mc admin cluster iam export` 命令分别对存储桶元数据和 IAM 配置进行快照。
必要时可使用这些快照恢复存储桶/IAM 设置，以便从用户或进程错误中恢复。

再平衡会忽略已过期对象和尾部 ``DeleteMarker``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

从 :minio-release:`RELEASE.2023-06-23T20-26-00Z` 开始，再平衡会忽略那些基于父存储桶已配置的 :ref:`生命周期规则 <minio-lifecycle-management-expiration>` 而过期的对象版本。

再平衡还会忽略仅剩余版本为 :ref:`删除标记 <minio-bucket-versioning-delete>` 的对象。
这可避免对已被视为完全删除的对象执行跨池 :abbr:`I/O (Input/Output)`。

MinIO 依赖 :ref:`scanner <minio-concepts-scanner>` 来识别并清理这些过期对象或尾部 ``DeleteMarker`` 对象。
