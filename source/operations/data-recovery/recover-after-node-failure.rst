.. _minio-restore-hardware-failure-node:

============
节点故障恢复
============

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 1

如果某个 MinIO 节点发生完全硬件故障（例如所有驱动器、数据等全部丢失），则该节点在重新加入部署后会开始执行 :ref:`自愈操作 <minio-concepts-healing>`。
MinIO 自愈仅发生在被替换的硬件上，通常不会影响部署性能。

MinIO 自愈会确保恢复到驱动器上的所有数据保持一致且正确。

.. include:: /includes/common-admonitions.rst
   :start-after: start-exclusive-drive-access
   :end-before: end-exclusive-drive-access

替换节点的硬件应与故障节点大体相近。
使用更好的硬件不会带来负面性能影响。

替换驱动器的硬件也应与故障驱动器大体相近。
例如，应使用相同容量的另一块 SSD 来替换故障 SSD。
虽然你可以使用容量更大的驱动器，但 MinIO 会以 :ref:`服务器池 <minio-intro-server-pool>` 中 *最小* 驱动器的容量，作为该 pool 内所有驱动器的上限。

以下步骤提供了更详细的节点替换流程。
这些步骤假定你使用的是一个 MinIO 部署，其中每个节点都按照 :ref:`文档中的前置条件 <minio-installation>` 配置了 DNS 主机名。

1) 启动替换节点
----------------

请确保新节点已经按照行业、监管或组织内部标准与要求，完成所有必要的安全、固件和操作系统更新。

新节点的软件配置 *必须* 与部署中其他节点保持一致，包括但不限于操作系统和内核版本及其配置。
异构软件配置可能导致部署中出现意料之外或不期望的行为。

2) 更新新节点的主机名解析
--------------------------------

*可选* 仅当替换节点的 IP 地址与故障主机不同时时，才需要执行此步骤。

确保原先关联到故障节点的主机名现在解析到新节点。

例如，如果 ``https://minio-1.example.net`` 之前解析到故障主机，那么它现在应解析到新主机。

3) 下载并准备 MinIO Server
--------------------------------

按照 :ref:`部署流程 <minio-installation>` 下载并运行 MinIO server，并使用与部署中其他节点一致的配置。

- 所有节点上的 MinIO server 版本 *必须* 一致
- 所有节点上的 MinIO service 与 environment file 配置 *必须* 一致

4) 将节点重新加入部署
------------------------

在该节点上启动 MinIO server 进程，并使用 :mc:`mc admin logs` 监控其输出；如果是 ``systemd`` 管理的安装，则可以使用 ``journalctl -u minio`` 监控 MinIO service 日志。

服务端输出应表明它已经检测到部署中的其他节点，并开始执行 :ref:`自愈操作 <minio-concepts-healing>`。

使用 :mc:`mc admin heal` 监控部署整体的自愈状态。
MinIO 会积极地对该节点执行自愈，以确保部署快速从降级状态恢复。

5) 后续步骤
-----------

继续监控部署，直到自愈完成。
如果部署持续或反复出现节点故障，应安排专项维护以定位根因。
可考虑使用 `MinIO SUBNET <https://min.io/pricing?jmp=docs>`__，与 MinIO Engineering 协作获取此类操作的指导。
