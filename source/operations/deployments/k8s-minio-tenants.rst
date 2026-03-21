============================
Kubernetes 上的 MinIO 租户
============================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 1

一个 MinIO 租户由部署在单个命名空间中的一整组 Kubernetes 资源组成，用于支撑 MinIO 对象存储服务。

本文默认目标 Kubernetes 基础设施上已经完成 :ref:`MinIO Operator 安装 <deploy-minio-operator>`。

前提条件
--------

你的 Kubernetes 基础设施必须满足以下前提，才能部署 MinIO 租户。

MinIO Kubernetes Operator
~~~~~~~~~~~~~~~~~~~~~~~~~

本页步骤 *要求* 已有一个有效的 MinIO Kubernetes Operator 安装，并默认本地主机也安装了与之匹配的 MinIO Kubernetes Operator。
本步骤默认使用最新稳定版 Operator，即版本 |operator-version-stable|。

有关部署 MinIO Operator 的完整文档，请参阅 :ref:`deploy-operator-kubernetes`。

带本地存储的 Worker Node
~~~~~~~~~~~~~~~~~~~~~~~~

MinIO **强烈建议** 将租户部署到带有本地直连存储的 Kubernetes worker node 上。

这些 Worker Node 应满足 MinIO 面向生产环境的 :ref:`硬件检查清单 <minio-hardware-checklist>`。

应避免将 MinIO 租户与其他高性能软件部署在同一 worker node 上；如果确有必要，则必须配置合适的限制和约束，以保证 MinIO 获得所需的计算与存储资源。

.. _deploy-minio-tenant-pv:

持久卷
~~~~~~

.. include:: /includes/common-admonitions.rst
   :start-after: start-exclusive-drive-access
   :end-before: end-exclusive-drive-access

MinIO 通常可以使用任何支持 :kube-docs:`ReadWriteOnce <concepts/storage/persistent-volumes/#access-modes>` 访问模式的 Kubernetes :kube-docs:`Persistent Volume (PV) <concepts/storage/persistent-volumes>`。
MinIO 的一致性保证依赖于 ``ReadWriteOnce`` 提供的独占存储访问。
此外，MinIO 建议将 PVC :kube-docs:`StorageClass <concepts/storage/storage-classes>` 的回收策略设置为 ``Retain``。
在条件允许的情况下，应配置 PV 底层的 Storage Class、CSI 或其他 provisioner 使用 XFS 格式化卷，以确保最佳性能。

对于节点具有 Direct Attached Storage 的 Kubernetes 集群，MinIO 强烈建议使用 `DirectPV CSI driver <https://min.io/directpv?ref=docs>`__。
DirectPV 提供了一个分布式持久卷管理器，可在 Kubernetes 节点之间发现、格式化、挂载、调度和监控驱动器。
DirectPV 解决了手动配置和监控 :kube-docs:`local persistent volumes <concepts/storage/volumes/#local>` 的局限性。

对于部署到 Amazon Elastic、Azure 或 Google Kubernetes 上的租户，请选择下方标签页查看针对 PV 配置的具体建议：

.. tab-set::

   .. tab-item:: Amazon EKS

      EKS 上的 MinIO 租户必须使用 :github:`EBS CSI Driver <kubernetes-sigs/aws-ebs-csi-driver>` 来配置所需的底层持久卷。
      MinIO 强烈建议使用基于 SSD 的 EBS 卷以获得最佳性能。
      MinIO 也强烈建议为基于 EBS 的 PV 使用 XFS 文件系统。
      请为 MinIO EBS PV 创建一个 StorageClass，并将 ``csi.storage.k8s.io/fstype`` `parameter <https://github.com/kubernetes-sigs/aws-ebs-csi-driver/blob/master/docs/parameters.md>`__ 设置为 ``xfs``。

      MinIO 推荐以下 :github:`EBS 卷类型 <kubernetes-sigs/aws-ebs-csi-driver/blob/master/docs/parameters.md>`：

      - ``io2`` (Provisioned IOPS SSD) **Preferred**
      - ``io1`` (Provisioned IOPS SSD)
      - ``gp3`` (General Purpose SSD)
      - ``gp2`` (General Purpose SSD)

      有关 EBS 资源的更多信息，请参阅 `EBS Volume Types <https://aws.amazon.com/ebs/volume-types/>`__。
      有关 StorageClass 参数的更多信息，请参阅 `StorageClass Parameters <https://github.com/kubernetes-sigs/aws-ebs-csi-driver/blob/master/docs/parameters.md>`__。

   .. tab-item:: Google GKS

      GKE 上的 MinIO 租户应使用 :gke-docs:`Compute Engine Persistent Disk CSI Driver <how-to/persistent-volumes/gce-pd-csi-driver>` 来配置所需的底层持久卷。

      MinIO 推荐以下 :gke-docs:`GKE CSI Driver <how-to/persistent-volumes/gce-pd-csi-driver>` 存储类：

      - ``standard-rwo`` (Balanced Persistent SSD)
      - ``premium-rwo`` (Performance Persistent SSD)

      MinIO 强烈建议使用基于 SSD 的磁盘类型以获得最佳性能。
      有关 GKE 磁盘类型的更多信息，请参阅 :gcp-docs:`Persistent Disks <disks>`。

   .. tab-item:: Azure AKS

      AKS 上的 MinIO 租户应使用 :azure-docs:`Azure Disks CSI driver <azure-disk-csi>` 来配置所需的底层持久卷。

      MinIO 推荐以下 :aks-docs:`AKS CSI Driver <azure-disk-csi>` 存储类：

      - ``managed-csi`` (Standard SSD)
      - ``managed-csi-premium`` (Premium SSD)

      MinIO 强烈建议使用基于 SSD 的磁盘类型以获得最佳性能。
      有关 AKS 磁盘类型的更多信息，请参阅 :azure-docs:`Azure disk types <virtual-machines/disk-types>`。

租户命名空间
~~~~~~~~~~~~

当你使用 Operator 创建租户时，该租户 *必须* 拥有自己的命名空间。
在该命名空间内，Operator 会根据租户配置生成所需的 pod。

每个租户 pod 运行三个容器：

- MinIO Container：运行所有标准 MinIO 功能，等价于裸机场景下的基础 MinIO 安装。
  该容器在提供的挂载点（持久卷）上存储和读取对象。

- InitContainer：仅在 pod 启动期间存在，用于在启动过程中管理配置 secret。
  启动完成后，该容器会终止。

- SideCar container：监控租户的配置 secret，并在其发生变化时进行更新。
  该容器还会检查 root 凭证，如果未找到 root 凭证则会产生错误。

从 v5.0.6 起，MinIO Operator 支持自定义 :kube-docs:`init containers <concepts/workloads/pods/init-containers>`，以满足特定环境所需的额外 pod 初始化逻辑。

租户通过 Persistent Volume Claim 与实际存储对象的 Persistent Volume 交互。

.. image:: /images/k8s/OperatorsComponent-Diagram.png
   :width: 600px
   :alt: 展示 MinIO Operator 使用或维护的命名空间和 pod 的示意图。
   :align: center

.. toctree::
   :titlesonly:
   :hidden:

   /operations/deployments/k8s-deploy-minio-tenant-on-kubernetes
   /operations/deployments/k8s-deploy-minio-tenant-helm-on-kubernetes
   /operations/deployments/k8s-modify-minio-tenant-on-kubernetes
   /operations/deployments/k8s-upgrade-minio-tenant-on-kubernetes
   /operations/deployments/k8s-expand-minio-tenant-on-kubernetes
   /operations/deployments/k8s-delete-minio-tenant-on-kubernetes
