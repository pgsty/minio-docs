.. _minio-availability-resiliency:

================
可用性与韧性
================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. meta::
   :keywords: availability, resiliency, healing, recovery, distributed
   :description: Information on MinIO Availability and Resiliency features in production environments

本页从生产视角概述 MinIO 在可用性与韧性方面的设计和特性。

.. note::

   本页内容旨在尽力帮助你理解 MinIO 在可用性与韧性方面的预期设计和理念。
   它不能替代 |subnet| 的能力。使用 |subnet| 可以在规划 MinIO 部署时与 MinIO Engineering 协同。

   社区用户可以通过 `MinIO Community Slack <https://slack.min.io>`__ 寻求支持。
   社区支持仅为 best-effort，不提供关于响应时间的 SLA。

分布式 MinIO 部署
-----------------------------

MinIO 将 :ref:`纠删码 <minio-erasure-coding>` 作为在驱动器级或节点级故障期间提供可用性与韧性的核心组件。
   MinIO 会将每个对象拆分为数据分片和 :ref:`校验 <minio-ec-parity>` 分片，并将这些分片分布到单个 :ref:`纠删码集合 <minio-ec-erasure-set>` 中。

   .. figure:: /images/availability/availability-erasure-sharding.svg
      :figwidth: 100%
      :align: center
      :alt: 纠删码对象被拆分为 12 个数据分片和 4 个校验分片的示意图

      这个小型单节点部署在一个纠删码集合中拥有 16 块驱动器。
      假设使用默认 :ref:`校验 <minio-ec-parity>` ``EC:4``，MinIO 会将对象拆分为 4 个校验分片和 12 个数据分片。
      MinIO 会将这些分片均匀分布到纠删码集合中的各块驱动器上。

MinIO 使用确定性算法为给定对象选择纠删码集合。
   对于每个唯一对象命名空间 ``BUCKET/PREFIX/[PREFIX/...]/OBJECT.EXTENSION``，MinIO 在读写操作中始终会选择相同的纠删码集合。
   这也包括同一对象的所有 :ref:`版本 <minio-bucket-versioning>`。

   .. figure:: /images/availability/availability-erasure-set-selection.svg
      :figwidth: 100%
      :align: center
      :alt: 基于对象命名空间选择纠删码集合的示意图

      MinIO 使用完整对象命名空间来计算目标纠删码集合。

MinIO 需要满足 :ref:`读写仲裁 <minio-read-quorum>` 才能对纠删码集合执行读写操作。
   仲裁取决于部署配置的校验值。
   读仲裁始终等于配置的校验值，因此只要某个纠删码集合丢失的驱动器数不超过校验值，MinIO 就可以对其执行读操作。

   .. figure:: /images/availability/availability-erasure-sharding-degraded.svg
      :figwidth: 100%
      :align: center
      :alt: 降级纠删码集合示意图，其中两个校验分片替换了两个数据分片

      该节点有两块驱动器故障。
      MinIO 会自动使用校验分片替换丢失的数据分片，并将重建后的对象返回给请求客户端。

   当默认校验值为 ``EC:4`` 时，部署中的每个纠删码集合最多可容忍 4 块驱动器丢失，同时仍能继续提供读操作。

写仲裁取决于配置的校验值和纠删码集合大小。
   如果校验值小于纠删码集合驱动器数量的 1/2，则写仲裁等于校验值，其行为与读仲裁相似。

   MinIO 会自动提高写入降级纠删码集合的对象校验值，以确保该对象能够满足与健康纠删码集合中对象相同的 :abbr:`SLA (Service Level Agreement)`。
   这种校验升级行为提供了额外的风险缓解层，但无法替代修复或更换损坏驱动器这一长期解决方案，后者才是让纠删码集合恢复到完全健康状态的根本方法。

   .. figure:: /images/availability/availability-erasure-sharding-degraded-write.svg
      :figwidth: 100%
      :align: center
      :alt: 降级纠删码集合示意图，其中两块驱动器发生故障

      该节点有两块驱动器故障。
      MinIO 会以升级后的 ``EC:6`` 校验值写入对象，以确保该对象满足与其他对象相同的 SLA。

   当默认校验值为 ``EC:4`` 时，部署中的每个纠删码集合最多可容忍 4 块驱动器丢失，同时仍能继续提供写操作。

如果校验值等于纠删码集合驱动器数量的 1/2，则写仲裁等于校验值加 1，以避免因 "split brain" 场景导致数据不一致。
   例如，如果纠删码集合中恰好一半驱动器因网络故障而被隔离，MinIO 会认为仲裁丢失，因为它无法为写操作建立一个 N+1 驱动器组。

   .. figure:: /images/availability/availability-erasure-sharding-split-brain.svg
      :figwidth: 100%
      :align: center
      :alt: 一半驱动器故障的纠删码集合示意图

      该节点有 50% 的驱动器故障。
      如果校验值为 ``EC:8``，该纠删码集合就无法满足写仲裁，MinIO 会拒绝对此集合的写操作。
      由于该纠删码集合仍然满足读仲裁，对现有对象的读操作仍可能成功。

纠删码集合如果永久丢失的驱动器数量超过配置校验值，就意味着已经发生数据丢失。
   对于最高校验配置，如果驱动器丢失数量等于校验值，纠删码集合就会进入 ``read only`` 模式。
   对于最大纠删码集合大小 16 和最大校验值 8 的情况，需要丢失 9 块驱动器才会发生数据丢失。

   .. figure:: /images/availability/availability-erasure-sharding-degraded-set.svg
      :figwidth: 100%
      :align: center
      :alt: 完全降级的纠删码集合示意图

      该纠删码集合丢失的驱动器数量超过了配置的 ``EC:4`` 校验值，因此已经同时失去读仲裁和写仲裁。
      MinIO 无法恢复存储在该纠删码集合中的任何数据。

   某些瞬时或临时驱动器故障，例如由存储控制器或连接硬件故障引起的情况，仍可能在该纠删码集合内恢复到正常运行状态。

MinIO 还会通过在 pool 的每个节点间对纠删码集合驱动器进行对称条带化，进一步降低纠删码集合故障风险。
   MinIO 会根据节点数和驱动器数自动计算最佳纠删码集合大小，其中最大集合大小为 16。
   随后，它会跨 pool 为每个纠删码集合从每个节点选择一块驱动器；如果纠删码集合条带大小大于节点数，则会循环选择。
   这种拓扑可对单节点故障，甚至该节点上的单个存储控制器故障提供韧性。

   .. figure:: /images/availability/availability-erasure-sharding-striped.svg
      :figwidth: 100%
      :align: center
      :alt: 一个由 16 节点、每节点 8 块驱动器组成的集群示意图，包含 8 个 16 驱动器纠删码集合，并在各节点上均匀条带化

      在此 16 x 8 部署中，MinIO 会计算出 8 个纠删码集合，每个集合 16 块驱动器。
      它会在所有可用节点上为每个纠删码集合分配每节点 1 块驱动器。
      如果只有 8 个节点，则 MinIO 需要为每个纠删码集合从每个节点选择 2 块驱动器。

   在上述拓扑中，该 pool 具有 8 个跨 16 个节点条带化的 16 驱动器纠删码集合。
   每个节点都会为每个纠删码集合分配 1 块驱动器。
   虽然丢失一个节点在技术上意味着丢失 8 块驱动器，但每个纠删码集合实际上只会各丢失 1 块驱动器。
   这使得即使节点停机，系统仍能保持仲裁。

同一 pool 中的每个纠删码集合彼此独立。
   即使一个纠删码集合完全降级，MinIO 仍可对其他纠删码集合执行读写操作。

   .. figure:: /images/availability/availability-erasure-set-failure.svg
      :figwidth: 100%
      :align: center
      :alt: 一个 MinIO 多 pool 部署中某个 pool 内一个纠删码集合故障的示意图

      一个 pool 中有一个降级纠删码集合。
      虽然 MinIO 已无法对该纠删码集合执行读写操作，但它仍能继续对该 pool 中健康的纠删码集合提供服务。

   不过，丢失的数据仍可能影响依赖 100% 数据可用性假设的工作负载。
   此外，每个纠删码集合都与其他集合完全独立，因此你不能使用其他纠删码集合来恢复一个完全降级的纠删码集合中的数据。
   你必须使用 :ref:`站点 <minio-site-replication-overview>` 或 :ref:`存储桶 <minio-bucket-replication>` 复制，创建一个具备 :abbr:`BC/DR (Business Continuity / Disaster Recovery)` 能力的远端部署，用于恢复丢失的数据。

对于多 pool MinIO 部署，每个 pool 都至少需要有一个纠删码集合保持读写仲裁，才能继续提供操作能力。
   如果某个 pool 丢失了全部纠删码集合，MinIO 就无法再判断某次读写操作原本是否应被路由到该 pool。
   因此，即使其他 pool 仍然可用，MinIO 也会停止整个部署中的所有 I/O。

   .. figure:: /images/availability/availability-pool-failure.svg
      :figwidth: 100%
      :align: center
      :alt: 一个 MinIO 多 pool 部署中某个 pool 故障的示意图

      该部署中的一个 pool 已完全故障。
      MinIO 无法再确定 I/O 应路由到哪个 pool 或纠删码集合。
      如果继续操作，就可能产生不一致状态，使对象及其版本分布在不同纠删码集合中。
      因此，MinIO 会暂停部署中的所有 :abbr:`I/O (Input/Output)`，直到该 pool 恢复。

   要恢复对该部署的访问，管理员必须将该 pool 恢复到正常运行状态。
   这可能需要根据故障严重程度执行磁盘格式化、硬件更换或节点更换。
   更完整的文档请参阅 :ref:`minio-restore-hardware-failure`。

   你可以使用复制远端将丢失数据恢复回该部署。
   存储在健康 pool 中的所有数据仍会安全保留在磁盘上。

.. include:: /includes/common-admonitions.rst
   :start-after: start-exclusive-drive-access
   :end-before: end-exclusive-drive-access

复制型 MinIO 部署
----------------------------

对于 MinIO 部署中发生的小规模或大规模数据丢失，MinIO 将 :ref:`站点复制 <minio-site-replication-overview>` 作为保证业务连续性和灾难恢复 (BC/DR) 的主要手段。
   .. figure:: /images/availability/availability-multi-site-setup.svg
      :figwidth: 100%
      :alt: 初始设置期间的多站点部署示意图

      每个对等站点都部署在独立数据中心，以防范大规模故障或灾难。
      如果某个数据中心完全离线，客户端可以故障切换到另一个站点。

MinIO 复制可以在站点因瞬时或持续停机而发生部分或全部数据丢失时，自动 :ref:`自愈 <minio-concepts-healing>` 该站点。
   .. figure:: /images/availability/availability-multi-site-healing.svg
      :figwidth: 100%
      :alt: 自愈过程中的多站点部署示意图

      Datacenter 2 曾经离线，Site B 需要重新同步。
      负载均衡器会将操作路由到 Datacenter 1 中的 Site A。
      Site A 会持续将数据复制到 Site B。

   一旦所有数据同步完成，你就可以恢复对该站点的正常连接。
   根据复制滞后量、站点间延迟以及整体工作负载的 :abbr:`I/O (Input / Output)` 情况，你可能需要临时停止写操作，让各站点完全追平。

   如果某个对等站点彻底故障，你可以将其从配置中完全移除。
   负载均衡器配置也应移除该站点，以避免将客户端请求路由到离线站点。

   然后，你可以在修复原始硬件或完全更换硬件后，通过 :ref:`将其重新加入站点复制配置 <minio-expand-site-replication>` 来恢复该对等站点。
   MinIO 会在持续复制新数据的同时，自动开始重同步已有数据。

站点在重同步期间仍可通过将 ``GET/HEAD`` 请求代理到健康对等站点来继续处理操作
   .. figure:: /images/availability/availability-multi-site-proxy.svg
      :figwidth: 100%
      :alt: 重同步期间的多站点部署示意图

      Site B 不具备所请求的对象，可能是由于复制滞后。
      它会将 ``GET`` 请求代理到 Site A。
      Site A 返回对象后，Site B 再将其返回给请求客户端。

   客户端会收到首个返回所请求对象任意版本的对等站点结果。

   ``PUT`` 和 ``DELETE`` 操作通过常规复制流程进行同步。
   ``LIST`` 操作不会代理，客户端必须只对健康对等站点发起这类请求。
