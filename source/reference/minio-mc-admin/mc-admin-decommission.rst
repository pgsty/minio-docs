.. _minio-mc-admin-decommission:

=========================
``mc admin decommission``
=========================

.. default-domain:: minio

.. contents:: Table of Contents
   :local:
   :depth: 2

.. mc:: mc admin decommission

语法
----

.. start-mc-admin-decommission-desc

:mc-cmd:`mc admin decommission` 命令用于启动 MinIO :ref:`server pools <minio-intro-server-pool>`
的下线流程。下线流程适用于移除较旧的服务器池，这些服务器池的硬件能力或性能已不再满足要求，
或相较部署中的其他池表现不足。MinIO 会根据每个池可用空闲空间的比例，自动将数据从被下线
的池迁移到部署中其余的池。

.. end-mc-admin-decommission-desc

有关服务器池下线的完整操作流程，请参阅 :ref:`minio-decommissioning`。

.. admonition:: 下线是永久性的
   :class: important

   一旦 MinIO 开始下线某个池，就会将该池标记为*永久*非活动状态（“draining”）。
   取消或以其他方式中断下线流程都**不会**将该池恢复为活动状态。

   下线是一项重要的管理操作，规划和执行都需要谨慎处理，不是轻量或“日常”任务。

   `MinIO SUBNET <https://min.io/pricing?jmp=docs>`__ 用户可以
   `log in <https://subnet.min.io/>`__ 并创建与下线相关的新 issue。通过 SUBNET 与
   MinIO Engineering 协同可提高下线成功率，包括性能测试和健康诊断。

   社区用户可在 `MinIO Community Slack
   <https://slack.min.io>`__ 寻求支持。社区支持仅为 best-effort，不提供响应 SLA。

.. code-block:: shell

   mc admin [GLOBALFLAGS] decommission start|status|cancel ALIAS TARGET

参数
~~~~

.. mc-cmd:: start

   *必需* 启动 :mc-cmd:`~mc admin decommission TARGET` 指定服务器池的下线流程。

   必须指定 :mc-cmd:`~mc admin decommission TARGET`

.. mc-cmd:: status

   *必需* 返回指定 :mc-cmd:`~mc admin decommission ALIAS` 上所有服务器池的下线状态：

   - :guilabel:`Active` - 该池处于活动状态，且未计划下线。

   - :guilabel:`Draining` - 该池当前正在下线。

   - :guilabel:`Draining (Failed)` - 下线流程失败，需要手动重启流程。

   - :guilabel:`Draining (Cancelled)` - 下线流程已被手动取消。

   如果命令包含 :mc-cmd:`~mc admin decommission TARGET`，则在下线进行中时，
   命令输出会包含数据迁移速率。

.. mc-cmd:: cancel

   *必需* 取消 :mc-cmd:`~mc admin decommission TARGET` 指定池上的进行中下线流程。

   必须指定 :mc-cmd:`~mc admin decommission TARGET`。

   取消下线流程不会使该池恢复为活动状态。最终仍必须完成下线流程并将该池从部署中移除。
   可以再次对该池运行 :mc-cmd:`mc admin decommission start` 以恢复流程。

.. mc-cmd:: ALIAS

   *必需* 要启动下线流程的 MinIO 部署的 :ref:`alias <alias>`。

.. mc-cmd:: TARGET

   命令所操作服务器池的完整描述。例如：

   .. code-block:: shell

      https://minio-{01...04}.example.net:9000/mnt/disk{1...4}



全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

有关服务器池下线的完整操作流程，请参阅 :ref:`minio-decommissioning`。
