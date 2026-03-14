.. _minio-erasure-coding:

==============
纠删码
==============

.. default-domain:: minio

.. container:: extlinks-video

   - `MinIO 纠删码概览 <https://www.youtube.com/watch?v=QniHMNNmbfI>`__

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. meta::
   :keywords: erasure coding, healing, availability, resiliency
   :description: Information on MinIO Erasure Coding

MinIO 将纠删码作为提供数据冗余和可用性的核心组件。
本页介绍 MinIO 的纠删码机制。

有关 MinIO 如何在生产部署中使用纠删码的更多信息，请参阅 :ref:`minio-availability-resiliency` 和 :ref:`minio-architecture`。


.. _minio-ec-basics:
.. _minio-ec-erasure-set:
.. _minio-read-quorum:

纠删码基础
---------------------

.. note::

   本节中的图示和内容仅用于说明 MinIO 纠删码操作的简化视图，并不完整呈现 MinIO 纠删码实现的全部复杂性。

MinIO 会将每个 :term:`服务器池` 中的驱动器分组为一个或多个相同大小的 **纠删码集合**。
   .. figure:: /images/erasure/erasure-coding-erasure-set.svg
      :figwidth: 100%
      :align: center
      :alt: Diagram of erasure set covering 4 nodes and 16 drives

      上述示例部署由 4 个节点组成，每个节点有 4 块驱动器。
      MinIO 初始化后会生成一个纠删码集合，覆盖这 4 个节点上的全部 16 块驱动器。

   MinIO 会在初始化 :term:`服务器池` 时确定纠删码集合的最佳数量和大小。
   初次设置完成后，便无法再修改这些参数。

对于每次写操作，MinIO 都会将对象切分为 **数据** 和 **校验** 分片。
   纠删码集合的条带大小决定了部署可用的最大 :ref:`校验 <minio-ec-parity>` 值。
   生成数据分片和校验分片数量的公式如下：

   .. code-block:: shell

      N (ERASURE SET SIZE) = K (DATA) + M (PARITY)

   .. figure:: /images/erasure/erasure-coding-possible-parity.svg
      :figwidth: 100%
      :align: center
      :alt: Diagram of possible erasure set parity settings

      上述示例部署的纠删码集合包含 16 块驱动器。
      这意味着它支持从 ``EC:0`` 到纠删码集合驱动器数量 1/2 的校验值，也就是最高 ``EC:8``。

你可以将校验值设置在 0 到纠删码集合大小的 1/2 之间。
   .. figure:: /images/erasure/erasure-coding-erasure-set-shard-distribution.svg
      :figwidth: 100%
      :align: center
      :alt: Diagram of an object being sharded using MinIO's Reed-Solomon Erasure Coding algorithm.

      MinIO 使用 Reed-Solomon 纠删码实现来切分对象，并将其分布到纠删码集合中。
      上述示例部署的纠删码集合大小为 16，校验值为 ``EC:4``。

   对象一旦按某个校验设置写入，即使之后修改校验值，也不会自动更新。

MinIO 至少需要 ``K`` 个任意类型的分片才能读取对象。
   这里的 ``K`` 构成部署的读仲裁。
   因此，纠删码集合中至少要有 ``K`` 块健康驱动器，才能支持读操作。

   .. figure:: /images/erasure/erasure-coding-shard-read-quorum.svg
      :figwidth: 100%
      :align: center
      :alt: Diagram of a 4-node 16-drive deployment with one node offline.

      该部署中有一个节点离线，因此只剩下 12 块健康驱动器。
      该对象按 ``EC:4`` 写入，其读仲裁为 ``K=12``。
      因此该对象仍满足读仲裁，MinIO 可以重建对象并响应读操作。

   对于已经失去读仲裁的对象，MinIO 无法进行重建。
   这类对象可能需要通过其他方式恢复，例如 :ref:`复制重同步 <minio-bucket-replication-resynchronize>`。

MinIO 至少需要 ``K`` 块纠删码集合驱动器才能写入对象。
   这里的 ``K`` 构成部署的写仲裁。
   因此，纠删码集合中至少要有 ``K`` 块可用驱动器在线，才能支持写操作。

   .. figure:: /images/erasure/erasure-coding-shard-write-quorum.svg
      :figwidth: 100%
      :align: center
      :alt: Diagram of a 4-node 16-drive deployment where one node is offline.

      该部署中有一个节点离线，因此只剩下 12 块健康驱动器。
      客户端按 ``EC:4`` 校验设置写入对象，此时该纠删码集合的写仲裁为 ``K=12``。
      该纠删码集合仍满足写仲裁，因此 MinIO 可以用它执行写操作。

当校验 ``EC:M`` 恰好等于纠删码集合大小的 1/2 时，写仲裁为 ``K+1``。
   这样可以防止 split-brain 场景，例如网络故障导致纠删码集合中的一半驱动器与另一半完全隔离。
   
   .. figure:: /images/erasure/erasure-coding-shard-split-brain.svg
      :figwidth: 100%
      :align: center
      :alt: Diagram of an erasure set with where Parity ``EC:M`` is 1/2 the set size

      该部署中有两个节点因临时网络故障而离线。
      客户端按 ``EC:8`` 校验设置写入对象，此时该纠删码集合的写仲裁为 ``K=9``。
      该纠删码集合已经失去写仲裁，因此 MinIO 无法将其用于写操作。

   ``K+1`` 逻辑可确保客户端不会把同一个对象分别写入纠删码集合的两个“半边”，从而避免潜在的不一致。

对于仍满足读仲裁的对象，MinIO 可以使用任意数据分片或校验分片来 :ref:`自愈 <minio-concepts-healing>` 受损分片。
   .. figure:: /images/erasure/erasure-coding-shard-healing.svg
      :figwidth: 100%
      :align: center
      :alt: Diagram of MinIO using parity shards to heal lost data shards on a node.

      一个按 ``EC:4`` 写入的对象由于驱动器故障，在 12 个数据分片中丢失了 4 个。
      由于该对象仍满足读仲裁，MinIO 可以使用现有的校验分片对丢失的数据分片执行自愈。

你可以使用 MinIO `纠删码计算器 <https://min.io/product/erasure-code-calculator>`__，为计划中的拓扑探索可能的纠删码集合大小和分布方式。
如无特殊原因，建议使用偶数个节点和每节点偶数块驱动器，以简化拓扑规划以及驱动器和纠删码集合分布的理解。

.. include:: /includes/common-admonitions.rst
   :start-after: start-exclusive-drive-access
   :end-before: end-exclusive-drive-access

.. _minio-ec-parity:

纠删码校验与存储效率
-------------------------------------

为部署设置校验值，本质上是在可用性与总可用存储之间做平衡。
更高的校验值会提高对驱动器或节点故障的容忍度，但会减少可用存储；更低的校验值则提供更高的可用存储，但对驱动器或节点故障的容忍度也更低。
你可以使用 MinIO `纠删码计算器 <https://min.io/product/erasure-code-calculator?ref=docs>`__，查看不同校验值对计划中集群部署的影响。

下表列出了由 1 个节点和 16 块 1TB 驱动器组成的 MinIO 部署在不同纠删码校验级别下的结果：

.. list-table:: 16 驱动器 MinIO 集群中的校验设置结果
   :header-rows: 1
   :widths: 20 20 20 20 20
   :width: 100%

   * - 校验
     - 总存储
     - 存储比例
     - 读操作所需最少驱动器
     - 写操作所需最少驱动器

   * - ``EC: 4`` (默认)
     - 12 Tebibytes
     - 0.750
     - 12
     - 12

   * - ``EC: 6``
     - 10 Tebibytes
     - 0.625
     - 10
     - 10

   * - ``EC: 8``
     - 8 Tebibytes
     - 0.500
     - 8
     - 9

.. _minio-ec-bitrot:

位腐化防护
------------------

`Bit rot <https://en.wikipedia.org/wiki/Data_degradation>`__ 是一种静默数据损坏，由存储介质层面的随机变化引起。
对于数据驱动器而言，它通常来自表示数据的电荷衰减或磁取向变化。
成因可以从停电时的微小电流尖峰，到导致比特翻转的随机宇宙射线。
这种“bit rot”会在不触发监控工具或硬件告警的情况下，对数据介质造成细微错误或损坏。

MinIO 对 :minio-git:`HighwayHash algorithm <highwayhash/blob/master/README.md>` 的优化实现，确保其能够在运行时捕获并自愈受损对象。
它通过在 READ 时计算哈希并在 WRITE 时进行校验，确保从应用、网络到内存或驱动器的端到端完整性。
该实现专为速度设计，在 Intel CPU 上单核即可达到超过 10 GB/sec 的哈希速度。
