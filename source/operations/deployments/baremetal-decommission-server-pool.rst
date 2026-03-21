.. _minio-decommissioning:

=====================
退役 服务器池
=====================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 1

MinIO 支持从包含两个或更多 pool 的部署中退役并移除 :ref:`服务器池s <minio-intro-server-pool>`。
执行退役前，至少必须保留一个具有足够可用空间的 pool，以接收被退役 pool 中的对象。

从 ``RELEASE.2023-01-18T04-36-38Z`` 起，MinIO 支持在一条退役命令中排队 :ref:`多个 pool <minio-decommission-multiple-pools>`。
每个被列出的 pool 会立即进入只读状态，但实际排空过程一次只处理一个 pool。

退役功能适用于移除那些相较于当前部署中其他 pool 而言，硬件能力或性能已经不足的旧 服务器池。
MinIO 会根据各 pool 的空闲空间占比，自动将被退役 pool 中的数据迁移到部署中其余 pool。

在退役过程中，MinIO 会像平常一样路由读取操作（例如 ``GET``、``LIST``、``HEAD``）。
写入操作（例如 ``PUT``、带版本的 ``DELETE``）会被路由到部署中其余仍处于“active”状态的 pool。
带版本对象在迁移过程中会保持其顺序。

本页步骤用于从至少包含两个 服务器池 的 :ref:`分布式 <deploy-minio-distributed>` MinIO 部署中退役并移除一个或多个 服务器池。

.. admonition:: 退役是永久性的
   :class: important

   一旦 MinIO 开始退役某个 pool，就会将其标记为 *永久* 非活跃（“draining”）状态。
   取消或以其他方式中断退役流程，**不会** 将该 pool 恢复为 active 状态。
   退役多个 pool 时必须格外谨慎。

   退役是一项重大的管理操作，规划与执行都需要谨慎对待，并不是轻量或“日常型”的任务。

   `MinIO SUBNET <https://min.io/pricing?jmp=docs>`__ 用户可以 `登录 <https://subnet.min.io/>`__ 并创建与退役相关的新工单。
   通过 SUBNET 与 MinIO Engineering 协作，可提高退役成功率，包括性能测试和健康诊断。

   社区用户可以在 `MinIO Community Slack <https://slack.min.io>`__ 寻求支持。
   社区支持仅为 best-effort，不对响应速度提供任何 SLA。


.. _minio-decommissioning-prereqs:

前提条件
--------

先备份集群设置
~~~~~~~~~~~~~~

在开始退役前，使用 :mc:`mc admin cluster bucket export` 和 :mc:`mc admin cluster iam export` 命令分别为存储桶元数据和 IAM 配置创建快照。
必要时，你可以使用这些快照恢复存储桶/IAM 设置，以从用户错误或流程错误中恢复。

网络与防火墙
~~~~~~~~~~~~

部署中的每个节点都应当与其他所有节点具备完整的双向网络访问能力。
对于容器化或编排式基础设施，这可能需要针对 Ingress、负载均衡器等网络和路由组件进行专门配置。
某些操作系统还可能需要设置防火墙规则。
例如，以下命令会在使用 ``firewalld`` 的服务器上显式开放 MinIO server 默认 API 端口 ``9000``：

.. code-block:: shell
   :class: copyable

   firewall-cmd --permanent --zone=public --add-port=9000/tcp
   firewall-cmd --reload

如果你为 :ref:`MinIO Console <minio-console>` 设置了静态端口（例如 ``:9001``），
则还必须允许外部客户端访问该端口，以确保连接可用。

MinIO **强烈建议** 使用负载均衡器管理到集群的连接。
由于部署中的任意 MinIO 节点都可以接收、转发或处理客户端请求，因此负载均衡器应采用 "Least Connections" 算法将请求路由到 MinIO 部署。

以下负载均衡器已知可与 MinIO 良好配合：

- `NGINX <https://www.nginx.com/products/nginx/load-balancing/>`__
- `HAProxy <https://cbonte.github.io/haproxy-dconv/2.3/intro.html#3.3.5>`__

如何配置防火墙或负载均衡器以支持 MinIO 不在本步骤范围内。

部署必须具备足够的存储空间
~~~~~~~~~~~~~~~~~~~~~~~~~~

退役过程会将目标 pool 中的对象迁移到部署中的其他 pool。
该部署的总可用存储空间 *必须* 大于被退役 pool 的总存储量。

使用 `纠删码计算器 <https://min.io/product/erasure-code-calculator>`__ 确认可用存储容量。
然后再减去部署中现有对象已占用的空间。

例如，假设某个部署的已用和空闲存储分布如下：

.. list-table::
   :stub-columns: 1
   :widths: 30 30 30
   :width: 100%

   * - Pool 1
     - 100TB 已用
     - 200TB 总计

   * - Pool 2
     - 100TB 已用
     - 200TB 总计

   * - Pool 3
     - 100TB 已用
     - 200TB 总计

退役 Pool 1 需要将这 100TB 已用存储分散到其余 pool 上。
Pool 2 和 Pool 3 各自都有 100TB 未使用存储空间，因此可以安全接收 Pool 1 上的数据。

但如果 Pool 1 已满（例如已使用 200TB），退役会将剩余 pool 完全填满，并可能阻止任何后续写入操作。

注意事项
--------

替换 服务器池
~~~~~~~~~~~~~~~~

对于通过新 pool 硬件替换旧 pool 硬件的升级周期，你应先通过 :ref:`扩容 <expand-minio-distributed>` 添加新 pool，再开始退役旧 pool。
先添加新 pool，有助于退役过程在所有可用 pool（包括现有 pool 和新 pool）之间更均衡地迁移对象。

在退役旧硬件 pool 之前，请先完成所有计划中的 :ref:`硬件扩容 <expand-minio-distributed>`。

退役要求集群拓扑在整个 pool 排空过程中保持稳定。
**不要** 试图在同一步骤中同时执行扩容和退役变更。

退役可恢复继续执行
~~~~~~~~~~~~~~~~~~

如果退役因部署重启、网络故障等瞬时问题而中断，MinIO 会自动继续该过程。

对于人工取消或失败的退役尝试，只有在你手动重新发起退役操作后，MinIO 才会恢复执行。

无论中断原因为何，该 pool 都会持续保持在退役状态。
退役一旦开始，该 pool *永远* 无法恢复为 active 状态。

退役是无中断的
~~~~~~~~~~~~~~

移除已退役的 服务器池 需要在大致同一时间重启部署中的 *所有* MinIO 节点。

.. include:: /includes/common-installation.rst
   :start-after: start-nondisruptive-upgrade-desc
   :end-before: end-nondisruptive-upgrade-desc

退役会忽略已过期对象和尾部 ``DeleteMarker``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

从 :minio-release:`RELEASE.2023-05-27T05-56-19Z` 起，退役过程会忽略那些仅剩 ``DeleteMarker`` 版本的对象。
这样可以避免为实际上已被完全删除的对象，在剩余 服务器池 上生成空元数据。

从 :minio-release:`RELEASE.2023-06-23T20-26-00Z` 起，退役还会忽略那些已根据父存储桶配置的 :ref:`生命周期规则 <minio-lifecycle-management-expiration>` 过期的对象版本。
从 :minio-release:`RELEASE.2023-06-29T05-12-28Z` 起，你可以使用 :mc-cmd:`mc admin trace --call decommission <mc admin trace --call>` 在退役过程中监控被忽略的 delete marker 和已过期对象。

退役过程完成后，你就可以安全关闭该 pool。
由于剩余数据要么已计划删除，要么仅为 ``DeleteMarker``，因此你可以按照内部流程安全清空或销毁这些驱动器。

行为说明
--------

最终列表检查
~~~~~~~~~~~~

在退役过程结束时，MinIO 会检查该 pool 上是否还有对象列表。
如果列表为空，MinIO 会将退役标记为成功完成。
如果仍返回任何对象，MinIO 会报错，指出退役过程失败。

如果退役失败，客户应在重新尝试退役前先提交 |SUBNET| 工单以获取进一步协助。
没有 SUBNET 订阅的社区用户可以重试退役流程，或通过 `MinIO Community Slack <https://slack.min.io/>`__ 寻求额外支持。
MinIO 的社区支持仅为 best-effort，不提供任何响应速度方面的 :abbr:`SLA (Service Level Agreement)`。

对已启用 Tiering 的 Server 执行退役
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. versionchanged:: RELEASE.2023-03-20T20-16-18Z

对于已启用并处于活动状态的 tiering 部署，退役会将对象引用迁移到新的 active pool。
应用程序仍可继续对这些对象发出 GET 请求，MinIO 会透明地从远端 tier 中检索对象。

在旧版本 MinIO 中，tiering 配置会阻止退役操作。

.. _minio-decommissioning-server-pool:

退役单个 服务器池
--------------------

1) 查看 MinIO 部署拓扑
~~~~~~~~~~~~~~~~~~~~~~~

:mc:`mc admin decommission` 命令会返回 MinIO 部署中所有 pool 的列表：

.. code-block:: shell
   :class: copyable

   mc admin decommission status myminio

命令返回的输出类似如下：

.. code-block:: shell

   ┌─────┬────────────────────────────────────────────────────────────────┬──────────────────────────────────┬────────┐
   │ ID  │ Pools                                                          │ Capacity                         │ Status │
   │ 1st │ https://minio-{01...04}.example.com:9000/mnt/disk{1...4}/minio │  10 TiB (used) / 10  TiB (total) │ Active │
   │ 2nd │ https://minio-{05...08}.example.com:9000/mnt/disk{1...4}/minio │  60 TiB (used) / 100 TiB (total) │ Active │
   │ 3rd │ https://minio-{09...12}.example.com:9000/mnt/disk{1...4}/minio │  40 TiB (used) / 100 TiB (total) │ Active │
   └─────┴────────────────────────────────────────────────────────────────┴──────────────────────────────────┴────────┘

上例部署共有三个 pool。
每个 pool 都有四台服务器，每台服务器有四块驱动器。

确定要退役的目标 pool，并检查当前容量。
部署中其余 pool 的总容量 *必须* 足以迁移被退役 pool 中存储的所有对象。

在上述示例中，该部署总存储为 210TiB，其中已使用 110TiB。
第一个 pool（``minio-{01...04}``）是退役目标，因为它是在 MinIO 部署创建时配置的，并且已经完全写满。
其余较新的 pool 可以吸收该 pool 中的所有对象，而不会显著影响总可用存储。

2) 启动退役过程
~~~~~~~~~~~~~~~~

.. admonition:: 退役是永久性的
   :class: warning

   一旦 MinIO 开始退役某个 pool，就会将其标记为 *永久* 非活跃（“draining”）状态。
   取消或以其他方式中断退役流程，**不会** 将该 pool 恢复为 active 状态。

   在运行以下命令前，请先确认并验证你要退役的是正确的 pool。

使用 :mc-cmd:`mc admin decommission start` 命令开始退役目标 pool。
指定部署的 :ref:`alias <alias>`，以及待退役 pool 的完整描述，包括所有主机、磁盘和文件路径。

.. code-block:: shell
   :class: copyable

   mc admin decommission start myminio/ https://minio-{01...04}.example.net:9000/mnt/disk{1...4}/minio

该示例命令会在 ``myminio`` 部署上启动对匹配 服务器池 的退役。

在退役过程中，对于尚未迁移的对象，MinIO 会继续将读取操作（``GET``、``LIST``、``HEAD``）路由到该 pool。
所有新的写入操作（``PUT``）都会被路由到部署中其余 pool。

此时，负责管理部署连接的负载均衡器、反向代理或其他网络控制组件无需修改其配置。

3) 监控退役过程
~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc admin decommission status` 命令监控退役过程。

.. code-block:: shell
   :class: copyable

   mc admin decommission status myminio

命令返回的输出类似如下：

.. code-block:: shell

   ┌─────┬────────────────────────────────────────────────────────────────┬──────────────────────────────────┬──────────┐
   │ ID  │ Pools                                                          │ Capacity                         │ Status   │
   │ 1st │ https://minio-{01...04}.example.com:9000/mnt/disk{1...4}/minio │  10 TiB (used) / 10  TiB (total) │ Draining │
   │ 2nd │ https://minio-{05...08}.example.com:9000/mnt/disk{1...4}/minio │  60 TiB (used) / 100 TiB (total) │ Active   │
   │ 3rd │ https://minio-{09...12}.example.com:9000/mnt/disk{1...4}/minio │  40 TiB (used) / 100 TiB (total) │ Active   │
   └─────┴────────────────────────────────────────────────────────────────┴──────────────────────────────────┴──────────┘

你可以在命令中指定 服务器池 的描述，以获取更详细的信息：

.. code-block:: shell
   :class: copyable

   mc admin decommission status myminio https://minio-{01...04}.example.com:9000/mnt/disk{1...4}/minio

命令返回的输出类似如下：

.. code-block:: shell

   Decommissioning rate at 100MiB/sec [1TiB/10TiB]
   Started: 30 minutes ago

:mc-cmd:`mc admin decommission status` 会在退役完成后将 :guilabel:`Status` 标记为 :guilabel:`Complete`。
一旦退役完成，你就可以继续下一步。

如果 :guilabel:`Status` 显示为 failed，你可以重新运行 :mc-cmd:`mc admin decommission start` 命令以恢复该过程。
如果失败持续存在，请使用 :mc:`mc admin logs` 或查看 ``systemd`` 日志（例如 ``journalctl -u minio``），以定位更具体的错误。

4) 从部署配置中移除已退役的 Pool
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

每当一个 pool 完成退役后，你都可以安全地将其从部署配置中移除。
请修改部署中每个剩余 MinIO server 的启动命令，并移除已退役 pool。

``.deb`` 或 ``.rpm`` 软件包会将 `systemd <https://www.freedesktop.org/wiki/Software/systemd/>`__ 服务文件安装到 ``/lib/systemd/system/minio.service``。
对于二进制安装，本步骤默认该文件已按照 :ref:`deploy-minio-distributed` 流程手动创建。

``minio.service`` 文件使用位于 ``/etc/default/minio`` 的环境文件读取配置设置，其中也包括启动配置。
具体来说，``MINIO_VOLUMES`` 变量定义了启动命令：

.. code-block:: shell
   :class: copyable

   cat /etc/default/minio | grep "MINIO_VOLUMES"

命令返回的输出类似如下：

.. code-block:: shell

   MINIO_VOLUMES="https://minio-{1...4}.example.net:9000/mnt/disk{1...4}/minio https://minio-{5...8}.example.net:9000/mnt/disk{1...4}/minio https://minio-{9...12}.example.net:9000/mnt/disk{1...4}/minio"

编辑该环境文件，并从 ``MINIO_VOLUMES`` 的值中移除已退役 pool。

5) 更新网络控制平面
~~~~~~~~~~~~~~~~~~~~~~

更新所有负载均衡器、反向代理或其他网络控制平面，从 MinIO 部署的连接配置中移除已退役的 服务器池。

网络控制平面组件的具体配置说明不在本步骤范围内。

6) 重启 MinIO 部署
~~~~~~~~~~~~~~~~~~

在部署中的每个节点上**同时**执行以下命令，以重启 MinIO 服务：

.. include:: /includes/linux/common-installation.rst
   :start-after: start-install-minio-restart-service-desc
   :end-before: end-install-minio-restart-service-desc

.. include:: /includes/common-installation.rst
   :start-after: start-nondisruptive-upgrade-desc
   :end-before: end-nondisruptive-upgrade-desc

部署重新上线后，使用 :mc:`mc admin info` 确认部署中所有剩余 server 的运行状态。

.. _minio-decommission-multiple-pools:

退役多个 服务器池
--------------------

.. versionchanged:: RELEASE.2023-01-18T04-36-38Z

在执行退役命令时，你可以一次性启动多个 服务器池 的退役过程。

输入该命令后：

- MinIO 会立即停止对所有待退役 pool 的写访问。
- 退役一次只处理一个 pool。
- 每个 pool 完成排空后，MinIO 才会开始排空下一个 pool。

若要通过一条命令退役多个 服务器池，请将每个待退役 服务器池 的完整描述以逗号分隔列表的形式追加到命令中。

执行多 server 退役时，其他所有关于退役的注意事项同样适用。

- 退役是永久性的。
- 一旦将这些 pool 标记为退役，你就**无法**恢复它们。
- 请确认你选择的是预期的 pool。

1) 查看 MinIO 部署拓扑
~~~~~~~~~~~~~~~~~~~~~~~

:mc:`mc admin decommission` 命令会返回 MinIO 部署中所有 pool 的列表：

.. code-block:: shell
   :class: copyable

   mc admin decommission status myminio

命令返回的输出类似如下：

.. code-block:: shell

   ┌─────┬────────────────────────────────────────────────────────────────┬──────────────────────────────────┬────────┐
   │ ID  │ Pools                                                          │ Capacity                         │ Status │
   │ 1st │ https://minio-{01...04}.example.com:9000/mnt/disk{1...4}/minio │  10 TiB (used) / 10  TiB (total) │ Active │
   │ 2nd │ https://minio-{05...08}.example.com:9000/mnt/disk{1...4}/minio │  95 TiB (used) / 100 TiB (total) │ Active │
   │ 3rd │ https://minio-{09...12}.example.com:9000/mnt/disk{1...4}/minio │  40 TiB (used) / 500 TiB (total) │ Active │
   │ 4th │ https://minio-{13...16}.example.com:9000/mnt/disk{1...4}/minio │  0  TiB (used) / 500 TiB (total) │ Active │
   └─────┴────────────────────────────────────────────────────────────────┴──────────────────────────────────┴────────┘

上例部署共有三个 pool。
每个 pool 都有四台服务器，每台服务器有四块驱动器。

确定要退役的目标 pool，并检查当前容量。
部署中其余 pool 的总容量 *必须* 足以迁移被退役 pool 中存储的所有对象。

在上述示例中，该部署总存储为 1110TiB，其中已使用 145TiB。

- 第一个 pool（``minio-{01...04}``）是第一个退役目标，因为它是在 MinIO 部署创建时配置的，并且已经完全写满。
- 第二个 pool（``minio-{05...08}``）是第二个退役目标，因为它同样是在部署创建时配置的，并且已接近写满。
- 第四个 pool（``minio-{13...16}``）是一个刚通过 server 扩容加入的新硬件 pool。

第三个和第四个 pool 可以吸收第一个 pool 上的全部对象，而不会显著影响总可用存储。

.. important:: 

   在开始退役前，请先完成所有新增存储资源所需的 server 扩容。

2) 启动退役过程
~~~~~~~~~~~~~~~~

.. admonition:: 退役是永久性的
   :class: warning

   一旦 MinIO 开始退役这些 pool，就会将它们标记为 *永久* 非活跃（“draining”）状态。
   取消或以其他方式中断退役流程，**不会** 将这些 pool 恢复为 active 状态。

   在运行以下命令前，请先确认并验证你要退役的是正确的 pool。

使用 :mc-cmd:`mc admin decommission start` 命令开始退役目标 pool。
指定部署的 :ref:`alias <alias>`，以及以逗号分隔的每个待退役 pool 的完整描述，包括所有主机、磁盘和文件路径。

.. code-block:: shell
   :class: copyable

   mc admin decommission start myminio/ https://minio-{01...04}.example.net:9000/mnt/disk{1...4}/minio,https://minio-{05...08}.example.net:9000/mnt/disk{1...4}/minio

该示例命令会在 ``myminio`` 部署上启动对所列两个匹配 服务器池 的退役。

在退役过程中，对于尚未迁移的对象，MinIO 会继续将读取操作（``GET``、``LIST``、``HEAD``）路由到这些 pool。
所有新的写入操作（``PUT``）都会被路由到部署中那些未计划退役的其余 pool。

已退役 pool 的排空一次只处理一个 pool，并按顺序依次完成每个 pool 的退役。
排空过程 _不会_ 对所有待退役 pool 并发执行。

此时，负责管理部署连接的负载均衡器、反向代理或其他网络控制组件无需修改其配置。

3) 监控退役过程
~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc admin decommission status` 命令监控退役过程。

.. code-block:: shell
   :class: copyable

   mc admin decommission status myminio

命令返回的输出类似如下：

.. code-block:: shell

   ┌─────┬────────────────────────────────────────────────────────────────┬──────────────────────────────────┬──────────┐
   │ ID  │ Pools                                                          │ Capacity                         │ Status   │
   │ 1st │ https://minio-{01...04}.example.com:9000/mnt/disk{1...4}/minio │  10 TiB (used) / 10  TiB (total) │ Draining │
   │ 2nd │ https://minio-{05...08}.example.com:9000/mnt/disk{1...4}/minio │  95 TiB (used) / 100 TiB (total) │ Pending  │
   │ 3rd │ https://minio-{09...12}.example.com:9000/mnt/disk{1...4}/minio │  40 TiB (used) / 500 TiB (total) │ Active   │
   │ 4th │ https://minio-{13...16}.example.com:9000/mnt/disk{1...4}/minio │  0  TiB (used) / 500 TiB (total) │ Active   │
   └─────┴────────────────────────────────────────────────────────────────┴──────────────────────────────────┴──────────┘

你可以在命令中指定 服务器池 的描述，以获取更详细的信息：

.. code-block:: shell
   :class: copyable

   mc admin decommission status myminio https://minio-{01...04}.example.com:9000/mnt/disk{1...4}/minio

命令返回的输出类似如下：

.. code-block:: shell

   Decommissioning rate at 100MiB/sec [1TiB/10TiB]
   Started: 30 minutes ago

:mc-cmd:`mc admin decommission status` 会在退役完成后将 :guilabel:`Status` 标记为 :guilabel:`Complete`。
当 MinIO 完成所有 pool 的退役后，你就可以继续下一步。

如果 :guilabel:`Status` 显示为 failed，你可以重新运行 :mc-cmd:`mc admin decommission start` 命令以恢复该过程。
如果失败持续存在，请使用 :mc:`mc admin logs` 或查看 ``systemd`` 日志（例如 ``journalctl -u minio``），以定位更具体的错误。

4) 从部署配置中移除已退役的 Pool
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

退役完成后，你就可以安全地将这些 pool 从部署配置中移除。
请修改部署中每个剩余 MinIO server 的启动命令，并移除已退役 pool。

``.deb`` 或 ``.rpm`` 软件包会将 `systemd <https://www.freedesktop.org/wiki/Software/systemd/>`__ 服务文件安装到 ``/lib/systemd/system/minio.service``。
对于二进制安装，本步骤默认该文件已按照 :ref:`deploy-minio-distributed` 流程手动创建。

``minio.service`` 文件使用位于 ``/etc/default/minio`` 的环境文件读取配置设置，其中也包括启动配置。
具体来说，``MINIO_VOLUMES`` 变量定义了启动命令：

.. code-block:: shell
   :class: copyable

   cat /etc/default/minio | grep "MINIO_VOLUMES"

命令返回的输出类似如下：

.. code-block:: shell

   MINIO_VOLUMES="https://minio-{1...4}.example.net:9000/mnt/disk{1...4}/minio https://minio-{5...8}.example.net:9000/mnt/disk{1...4}/minio https://minio-{9...12}.example.net:9000/mnt/disk{1...4}/minio"

编辑该环境文件，并从 ``MINIO_VOLUMES`` 的值中移除已退役 pool。

5) 更新网络控制平面
~~~~~~~~~~~~~~~~~~~~~~

更新所有负载均衡器、反向代理或其他网络控制平面，从 MinIO 部署的连接配置中移除已退役的 服务器池。

网络控制平面组件的具体配置说明不在本步骤范围内。

6) 重启 MinIO 部署
~~~~~~~~~~~~~~~~~~

在部署中的每个节点上**同时**执行以下命令，以重启 MinIO 服务：

.. include:: /includes/linux/common-installation.rst
   :start-after: start-install-minio-restart-service-desc
   :end-before: end-install-minio-restart-service-desc

.. include:: /includes/common-installation.rst
   :start-after: start-nondisruptive-upgrade-desc
   :end-before: end-nondisruptive-upgrade-desc

部署重新上线后，使用 :mc:`mc admin info` 确认部署中所有剩余 server 的运行状态。
