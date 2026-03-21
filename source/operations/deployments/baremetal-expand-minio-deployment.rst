.. _expand-minio-distributed:

=================================
扩展分布式 MinIO 部署
=================================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 1

MinIO 支持通过添加新的 :ref:`服务器池 <minio-intro-server-pool>` 来扩展现有的分布式部署。
每个 Pool 都会扩展集群的总可用存储容量。

扩容并不能提供 Business Continuity/Disaster Recovery (BC/DR) 级别的保护。
虽然每个 pool 都是一组相互独立的服务器，并通过各自的 :ref:`纠删码集合 <minio-ec-erasure-set>` 提供可用性，但只要其中一个 pool 完全丢失，MinIO 就会停止该部署中所有 pool 的 I/O。
同样地，只要某个 pool 中的某个纠删码集合失去 quorum，该集合中存储的对象就会发生数据丢失，而不受其他纠删码集合或 pool 数量的影响。

新的 服务器池 **不必** 与现有任一 服务器池 使用相同类型或规格的硬件和软件配置，不过保持一致通常有助于简化集群管理，并让各 pool 间的性能表现更可预测。
新 pool 内的所有驱动器 **应当** 保持相同类型和容量。
有关如何选择合适配置的完整建议，请参阅 MinIO 的 :ref:`硬件建议 <minio-hardware-checklist>`。

若要为单 pool 或多 pool 的 MinIO 部署提供 BC/DR 级别的故障切换与恢复支持，请使用 :ref:`站点复制 <minio-site-replication-overview>`。

本页步骤用于为现有 :ref:`分布式 <deploy-minio-distributed>` MinIO 部署增加一个额外的 服务器池。

.. important::

   MinIO 不支持扩展 单机单盘 拓扑。

.. _expand-minio-distributed-prereqs:

前提条件
--------

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

部署中的所有 MinIO server *必须* 使用相同的监听端口。

如果你为 :ref:`MinIO Console <minio-console>` 设置了静态端口（例如 ``:9001``），
则还必须允许外部客户端访问该端口，以确保连接可用。

MinIO **强烈建议** 使用负载均衡器管理到集群的连接。
由于部署中的任意 MinIO 节点都可以接收、转发或处理客户端请求，因此负载均衡器应采用 "Least Connections" 算法将请求路由到 MinIO 部署。

以下负载均衡器已知可与 MinIO 良好配合：

- `NGINX <https://www.nginx.com/products/nginx/load-balancing/>`__
- `HAProxy <https://cbonte.github.io/haproxy-dconv/2.3/intro.html#3.3.5>`__

如何配置防火墙或负载均衡器以支持 MinIO 不在本步骤范围内。
:ref:`integrations-nginx-proxy` 参考页提供了一个将 NGINX 作为反向代理并启用基础负载均衡的基线配置。

连续主机名
~~~~~~~~~~

MinIO 在创建 服务器池 时，*要求* 使用扩展表示法 ``{x...y}`` 来表示一组连续的 MinIO 主机。
因此，MinIO *要求* 使用连续编号的主机名来表示 pool 中的每个 :mc:`minio server` 进程。

在开始本步骤之前，请先创建所需的 DNS 主机名映射。
例如，以下主机名可用于一个 4 节点分布式 服务器池：

- ``minio5.example.com``
- ``minio6.example.com``
- ``minio7.example.com``
- ``minio8.example.com``

你可以使用扩展表示法 ``minio{5...8}.example.com`` 来指定完整的主机名范围。

如何配置 DNS 以支持 MinIO 不在本步骤范围内。

存储要求
~~~~~~~~

.. |deployment| replace:: 服务器池

.. include:: /includes/common-installation.rst
   :start-after: start-storage-requirements-desc
   :end-before: end-storage-requirements-desc

.. include:: /includes/common-admonitions.rst
   :start-after: start-exclusive-drive-access
   :end-before: end-exclusive-drive-access

满足纠删码校验的最小驱动器数量
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

MinIO 要求每个 pool 都满足部署的 :ref:`纠删码 <minio-erasure-coding>` 设置。
具体来说，新 pool 的拓扑必须为每个 :ref:`纠删码集合 <minio-ec-erasure-set>` 提供至少 ``2 x EC:N`` 个驱动器，其中 ``EC:N`` 是该部署的 :ref:`Standard <minio-ec-storage-class>` 校验存储类。
这一要求可确保新的 服务器池 满足该部署预期的 :abbr:`SLA (Service Level Agreement)`。

你可以使用 `MinIO Erasure Code Calculator <https://min.io/product/erasure-code-calculator?ref=docs>`__ 检查新 pool 的 :guilabel:`Erasure Code Stripe Size (K+M)`。
如果列出的最大值至少为 ``2 x EC:N``，则该 pool 支持部署当前的纠删码校验设置。

时间同步
~~~~~~~~

多节点系统必须保持时间和日期同步，才能维持稳定的节点间操作与交互。
请确保所有节点都定期同步到同一时间服务器。
不同操作系统可采用不同方式同步时间和日期，例如 ``ntp``、``timedatectl`` 或 ``timesyncd``。

请查阅所用操作系统的文档，了解如何在各节点之间建立并维护准确且一致的系统时钟。

先备份集群设置
~~~~~~~~~~~~~~

在开始扩容前，使用 :mc:`mc admin cluster bucket export` 和 :mc:`mc admin cluster iam export` 命令分别为存储桶元数据和 IAM 配置创建快照。
必要时，你可以使用这些快照恢复 :ref:`存储桶 <minio-mc-admin-cluster-bucket-import>` 和 :ref:`IAM <minio-mc-admin-cluster-iam-import>` 设置，以从用户错误或流程错误中恢复。

注意事项
--------

.. _minio-writing-files:

写入文件
~~~~~~~~

MinIO 不会自动在新的 服务器池 之间重新平衡对象。
相反，MinIO 会根据某个 pool 的空闲空间占所有可用 pool 总空闲空间的比例，将新的写入操作分配到空闲空间最多的 pool。

用于计算某个 pool 被选中执行写操作概率的公式如下：

:math:`FreeSpaceOnPoolA / FreeSpaceOnAllPools`

假设有一个由三个 pool 组成的部署，总空闲空间为 10 TiB，分布如下：

- Pool A 有 3 TiB 空闲空间
- Pool B 有 2 TiB 空闲空间
- Pool C 有 5 TiB 空闲空间

MinIO 计算各 pool 被用于写入操作的概率如下：

- Pool A：30% (:math:`3TiB / 10TiB`)
- Pool B：20% (:math:`2TiB / 10TiB`)
- Pool C：50% (:math:`5TiB / 10TiB`)

除空闲空间计算外，如果某次写入（含校验）会导致驱动器使用率超过 99%，或已知剩余 inode 数量低于 1000，MinIO 也不会写入该 pool。

如有需要，你可以使用 :mc:`mc admin rebalance` 手动启动重平衡过程。
有关重平衡工作方式的更多信息，请参阅 :ref:`跨部署管理对象 <minio-rebalance>`。

同样地，MinIO 不会向正在退役中的 pool 执行写入。

扩容是无中断的
~~~~~~~~~~~~~~

新增 服务器池 需要在大致同一时间重启部署中的 *所有* MinIO server 进程。

.. include:: /includes/common-installation.rst
   :start-after: start-nondisruptive-upgrade-desc
   :end-before: end-nondisruptive-upgrade-desc

基于容量的规划
~~~~~~~~~~~~~~

MinIO 建议预先规划足以存放 **至少** 2 年数据的存储容量，并在使用率达到 70% 之前完成准备。
如果过于频繁地执行 :ref:`服务器池 扩容 <expand-minio-distributed>`，或按“just-in-time”方式扩容，通常意味着架构或规划存在问题。

例如，假设某套应用每年预计至少产生 100 TiB 数据，并计划在 3 年后再扩容。
初始 服务器池 为该部署提供了约 500 TiB 的可用存储，使集群能够在安全满足 70% 阈值的同时，为数据增长保留一定缓冲。
理想情况下，新 服务器池 **至少** 也应提供 500 TiB 的额外存储，以便在下次扩容前维持类似的生命周期。

由于 MinIO :ref:`纠删码 <minio-erasure-coding>` 需要预留部分存储用于校验，因此总 **原始** 存储容量必须高于计划中的 **可用** 容量。
建议使用 MinIO `纠删码计算器 <https://min.io/product/erasure-code-calculator>`__，围绕具体纠删码设置进行容量规划。

推荐的操作系统
~~~~~~~~~~~~~~

本教程默认所有运行 MinIO 的主机都使用 :ref:`推荐的 Linux 操作系统 <minio-installation-platform-support>`。

部署中的所有主机都应采用一致的 :ref:`软件配置 <minio-software-checklists>`。

.. _expand-minio-distributed-baremetal:

扩展分布式 MinIO 部署
---------------------

以下步骤将向现有 MinIO 部署中添加一个 :ref:`服务器池 <minio-intro-server-pool>`。
每个 Pool 都会在保持集群整体 :ref:`可用性 <minio-erasure-coding>` 的同时，扩展集群的总可用存储容量。

以下所有命令都使用示例值。
请将这些值替换为适用于你部署的实际值。

开始本步骤前，请先阅读 :ref:`expand-minio-distributed-prereqs`。

在 :ref:`退役旧硬件 pool <minio-decommissioning>` 之前，请先完成所有计划中的硬件扩容。

1) 在新 服务器池 的每个节点上安装 MinIO 二进制文件
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. include:: /includes/linux/common-installation.rst
   :start-after: start-install-minio-binary-desc
   :end-before: end-install-minio-binary-desc

2) 添加 TLS/SSL 证书
~~~~~~~~~~~~~~~~~~~~~~

.. include:: /includes/common-installation.rst
   :start-after: start-install-minio-tls-desc
   :end-before: end-install-minio-tls-desc

3) 创建 ``systemd`` 服务文件
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. include:: /includes/linux/common-installation.rst
   :start-after: start-install-minio-systemd-desc
   :end-before: end-install-minio-systemd-desc

4) 创建服务环境文件
~~~~~~~~~~~~~~~~~~~~

在 ``/etc/default/minio`` 创建环境文件。
MinIO 服务会将该文件作为 MinIO *以及* ``minio.service`` 文件所用全部 :ref:`环境变量 <minio-server-environment-variables>` 的来源。

以下示例假设：

- 该部署当前只有一个 服务器池，由四台使用连续主机名的 MinIO server 主机构成。

  .. code-block:: shell

     minio1.example.com   minio3.example.com   
     minio2.example.com   minio4.example.com   

  每台主机都有 4 块本地直连驱动器，挂载点连续：

  .. code-block:: shell

     /mnt/disk1/minio   /mnt/disk3/minio 
     /mnt/disk2/minio   /mnt/disk4/minio

- 新的 服务器池 由八台使用连续主机名的新 MinIO 主机构成：

  .. code-block:: shell

     minio5.example.com   minio9.example.com
     minio6.example.com   minio10.example.com
     minio7.example.com   minio11.example.com
     minio8.example.com   minio12.example.com

- 所有主机都具有八块本地直连驱动器，挂载点连续：

  .. code-block:: shell
     
     /mnt/disk1/minio  /mnt/disk5/minio
     /mnt/disk2/minio  /mnt/disk6/minio
     /mnt/disk3/minio  /mnt/disk7/minio
     /mnt/disk4/minio  /mnt/disk8/minio

- 该部署有一个运行在 ``https://minio.example.net`` 的负载均衡器，用于管理到所有 MinIO 主机的连接。
  在此步骤中，负载均衡器不应将请求路由到新主机，但应已准备好所需的配置更新计划。

请根据你的部署拓扑修改示例：

.. code-block:: shell
   :class: copyable

   # Set the hosts and volumes MinIO uses at startup
   # The command uses MinIO expansion notation {x...y} to denote a
   # sequential series. 
   # 
   # The following example starts the MinIO server with two 服务器池s.
   #
   # The space delimiter indicates a seperate 服务器池
   #
   # The second set of hostnames and volumes is the newly added pool.
   # The pool has sufficient stripe size to meet the existing erasure code
   # parity of the deployment (2 x EC:4)
   #
   # The command includes the port on which the MinIO servers listen for each
   # 服务器池.

   MINIO_VOLUMES="https://minio{1...4}.example.net:9000/mnt/disk{1...4}/minio https://minio{5...12}.example.net:9000/mnt/disk{1...8}/minio"

   # Set all MinIO server options
   #
   # The following explicitly sets the MinIO Console listen address to
   # port 9001 on all network interfaces. The default behavior is dynamic
   # port selection.

   MINIO_OPTS="--console-address :9001"

   # Set the root username. This user has unrestricted permissions to
   # perform S3 and administrative API operations on any resource in the
   # deployment.
   #
   # Defer to your organizations requirements for superadmin user name.

   MINIO_ROOT_USER=minioadmin

   # Set the root password
   #
   # Use a long, random, unique string that meets your organizations
   # requirements for passwords.

   MINIO_ROOT_PASSWORD=minio-secret-key-CHANGE-ME

你可以根据部署需要指定其他 :ref:`环境变量 <minio-server-environment-variables>` 或 server 命令行选项。
部署中的所有 MinIO 节点都应包含相同且取值一致的环境变量。

5) 使用扩展后的配置重启 MinIO 部署
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

在部署中的每个节点上**同时**执行以下命令，以重启 MinIO 服务：

.. include:: /includes/linux/common-installation.rst
   :start-after: start-install-minio-restart-service-desc
   :end-before: end-install-minio-restart-service-desc

.. include:: /includes/common-installation.rst
   :start-after: start-nondisruptive-upgrade-desc
   :end-before: end-nondisruptive-upgrade-desc

6) 后续步骤
~~~~~~~~~~~

- 更新所有负载均衡器、反向代理或其他网络控制平面，使客户端请求能够路由到 MinIO 分布式部署中的新主机。
  虽然 MinIO 会在内部自动管理路由，但由控制平面处理初始连接通常可以减少网络跳数并提升效率。

- 查看 :ref:`MinIO Console <minio-console>`，确认更新后的集群拓扑并监控性能。
