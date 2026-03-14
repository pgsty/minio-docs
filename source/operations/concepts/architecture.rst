.. _minio-architecture:

============
部署架构
============

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. meta::
   :keywords: topology, architecture, deployment, production
   :description: Information on MinIO Deployment architecture and topology in production environments

本页从生产视角概述 MinIO 的部署架构。
有关具体硬件或软件配置的信息，请参阅：

- :ref:`硬件检查清单 <minio-hardware-checklist>`
- :ref:`安全检查清单 <minio-security-checklist>`
- :ref:`软件检查清单 <minio-software-checklists>`
- :ref:`阈值与限制 <minio-server-limits>`

分布式 MinIO 部署
-----------------------------

生产级 MinIO 部署至少由 4 台具有同构存储和计算资源的 MinIO 主机构成。
   MinIO 会将这些资源聚合为一个 :ref:`pool <minio-intro-server-pool>`，并以单一对象存储服务的形式对外提供。

   .. figure:: /images/architecture/architecture-4-node-deploy.svg
      :figwidth: 100%
      :alt: 4 节点 MinIO 部署，存储和计算资源同构
      :align: center

      该 服务器池中的每台 MinIO 主机都具有一致的计算、存储和网络配置

使用本地直连存储时，MinIO 可提供最佳性能，例如连接到主机 PCI-E 控制器板上的 NVMe 或 SSD 驱动器。
   存储控制器应以 XFS 格式化驱动器并采用 "Just a Bunch of Drives" (JBOD) 配置呈现，不使用 RAID、池化或其他硬件/软件韧性层。
   MinIO 不建议使用缓存，无论是在驱动器层还是控制器层。
   任意一种缓存都会在填充和清空时导致 :abbr:`I/O (Input / Output)` 尖峰，从而带来不可预测的性能。

   .. figure:: /images/architecture/architecture-one-node-DAS.svg
      :figwidth: 100%
      :alt: 通过 SAS 连接到 PCI-E 存储控制器的直连存储 MinIO Server 示意图
      :align: center

      每块 SSD 都通过 SAS 连接到以 HBA 模式运行的 PCI-E 直连存储控制器

MinIO 会自动将 服务器池中的驱动器分组为 :ref:`纠删码集合 <minio-ec-erasure-set>`。
   纠删码集合是 MinIO :ref:`可用性与韧性 <minio-availability-resiliency>` 的基础组件。
   MinIO 会在 服务器池的各节点间对纠删码集合进行对称条带化，以保持纠删码集合驱动器分布均匀。
   随后，MinIO 会根据部署的 :ref:`校验 <minio-ec-parity>` 将对象拆分为数据分片和校验分片，并将其分布到纠删码集合中。

   如需更完整地了解 MinIO 的冗余和自愈机制，请参阅 :ref:`minio-erasure-coding` 和 :ref:`minio-concepts-healing`。

   .. figure:: /images/architecture/architecture-erasure-set-shard.svg
      :figwidth: 100%
      :alt: 对象被拆分为 8 个数据块和 8 个校验块，并分布到 16 块驱动器上的示意图
      :align: center

      当校验值为最高的 ``EC:8`` 时，MinIO 会将对象切分为 8 个数据块和 8 个校验块，并分布到纠删码集合中的各块驱动器上。
      此 服务器池中的所有纠删码集合都具有相同的条带大小和分片分布。

MinIO 使用基于对象名称和路径的确定性哈希算法，为给定对象选择纠删码集合。
   对于每个唯一对象命名空间 ``BUCKET/PREFIX/[PREFIX/...]/OBJECT.EXTENSION``，MinIO 在读写操作中始终会选择相同的纠删码集合。
   MinIO 会处理 服务器池和纠删码集合内部的所有路由，使选择、读取和写入过程对应用完全透明。

   .. figure:: /images/architecture/architecture-erasure-set-retrieve-object.svg
      :figwidth: 100%
      :alt: 仅从数据分片恢复对象的示意图
      :align: center

      MinIO 会在将对象返回给请求客户端之前，透明地从数据分片或校验分片重建对象。

每个 MinIO server 都完整掌握分布式拓扑，因此应用可以连接到部署中的任意节点并对其发起操作。
   响应请求的 MinIO 节点会自动处理对部署中其他节点的内部路由，并将最终响应返回给客户端。

   应用通常不应自行管理这些连接，因为部署拓扑的任何变化都可能要求应用随之更新。
   生产环境应部署负载均衡器或类似网络控制平面组件，以管理到 MinIO 部署的连接。
   例如，你可以部署 NGINX 负载均衡器，对部署中的可用节点执行 "least connections" 或 "round robin" 负载均衡。

   .. figure:: /images/architecture/architecture-load-balancer-8-node.svg
      :figwidth: 100%
      :alt: 位于负载均衡器后的 8 节点 MinIO 部署示意图
      :align: center

      负载均衡器会将请求路由到部署中的任意节点。
      之后由接收请求的节点处理所有节点间请求。

你可以通过 :ref:`pool 扩容 <expand-minio-distributed>` 增加 MinIO 部署的可用存储。
   每个 服务器池都是由自身纠删码集合组成的独立节点组。
   MinIO 必须查询每个 服务器池，才能确定读写操作应定向到哪个纠删码集合，因此每增加一个 服务器池，每次调用的节点间流量都会相应增加。
   包含目标纠删码集合的 服务器池随后会响应该操作，而这一过程对应用完全透明。

   如果你通过 服务器池扩容修改了 MinIO 拓扑，可以通过更新负载均衡器，将新 服务器池的节点纳入应用流量。
   应用仍可继续使用该 MinIO 部署的负载均衡器地址，而无需更新或修改。
   这样既能保证请求在所有 服务器池之间均匀分布，又能让应用继续使用单一负载均衡器 URL 访问 MinIO。

   .. figure:: /images/architecture/architecture-load-balancer-multi-pool.svg
      :figwidth: 100%
      :alt: 位于负载均衡器后的多 服务器池MinIO 部署示意图
      :align: center

      PUT 请求需要检查每个 服务器池，以确定目标纠删码集合。
      一旦识别完成，MinIO 就会切分对象，并将数据分片和校验分片分布到对应集合中。

客户端应用可以使用任意兼容 S3 的 SDK 或库与 MinIO 部署交互。
   MinIO 也发布了专门面向兼容 S3 部署使用的 :ref:`SDK <minio-drivers>`。

   .. figure:: /images/architecture/architecture-multiple-clients.svg
      :figwidth: 100%
      :alt: 多个兼容 S3 的客户端通过 SDK 连接到 MinIO 的示意图

      使用不同兼容 S3 SDK 的客户端可以对同一个 MinIO 部署执行操作。

   MinIO 严格实现 S3 API，包括要求客户端对所有操作使用 AWS :s3-api:`Signature V4 <sig-v4-authenticating-requests.html>` 或 legacy Signature V2 进行签名。
   AWS 签名计算依赖客户端提供的 header，因此负载均衡器、代理、安全程序或其他组件若修改这些 header，就会导致签名不匹配错误并使请求失败。
   请确保任何此类中间组件都支持将 header 从客户端到服务器原样透传。

   虽然 S3 API 的所有操作都使用 ``GET`` 和 ``POST`` 等 HTTP 方法，应用通常仍会使用 SDK 来执行 S3 操作。
   特别是签名计算的复杂性，通常使通过 ``curl`` 或类似 REST 客户端直接交互变得不切实际。
   MinIO 建议使用能够自动完成签名计算的兼容 S3 SDK 或库。

.. _minio-deployment-architecture-replicated:

复制型 MinIO 部署
----------------------------

MinIO :ref:`站点复制 <minio-site-replication-overview>` 支持在彼此独立的部署之间进行同步。
   你可以将对等站点部署在不同机架、数据中心或地理区域，以支持 :abbr:`BC/DR (Business Continuity / Disaster Recovery)`，或为全球分布式 MinIO 对象存储提供地理就近的读写性能。

   .. figure:: /images/architecture/architecture-multi-site.svg
      :figwidth: 100%
      :alt: 含三个 MinIO 对等站点的多站点部署示意图

      一个包含三个对等站点的 MinIO 多站点部署。
      写入某个对等站点的操作会自动复制到配置中的其他所有对等站点。

复制性能主要取决于各对等站点之间的网络延迟。
   当对等站点跨地理区域分布时，站点间的高延迟可能导致显著复制滞后。
   如果工作负载已经接近或达到部署的总体性能上限，这一问题会进一步放大，因为复制过程本身也需要足够的空闲 :abbr:`I/O (Input / Output)` 来同步对象。

   .. figure:: /images/architecture/architecture-multi-site-latency.svg
      :figwidth: 100%
      :alt: 含站点间延迟的多站点部署示意图

      在此对等配置中，站点 A 与其他对等站点之间的延迟为 100ms。
      对象最早也要在 110ms 之后才能完成对所有站点的同步。

部署支持站点间故障切换协议的全局负载均衡器或类似网络设备，是多站点部署正常工作的关键。
   负载均衡器应支持 health probe/check 设置，以检测某个站点故障，并自动将应用重定向到其他健康对等站点。

   .. figure:: /images/architecture/architecture-load-balancer-multi-site.svg
      :figwidth: 100%
      :alt: 含两个站点的站点复制部署示意图

      负载均衡器会根据配置逻辑（地理就近、延迟等）自动路由客户端请求。
      写入某个站点的数据会自动复制到另一个对等站点。

   在连接均衡和 header 保留方面，负载均衡器应满足与单站点部署相同的要求。
   MinIO 复制会通过排队对象来处理瞬时故障。
