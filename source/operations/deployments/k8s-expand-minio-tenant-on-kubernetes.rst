.. _minio-k8s-expand-minio-tenant:

=====================
扩展 MinIO Tenant
=====================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 1


本步骤说明如何通过在 Kubernetes 基础设施中部署额外的一组 MinIO pod，来扩展现有 MinIO tenant 的可用存储容量。

.. important::

   MinIO Operator Console 已被弃用，并在 Operator 6.0.0 中移除。

   有关将通过 Operator Console 安装的 Tenant 迁移到 Kustomization 的说明，请参阅 :ref:`minio-k8s-modify-minio-tenant`。

前提条件
--------

MinIO Kubernetes Operator
~~~~~~~~~~~~~~~~~~~~~~~~~

本页步骤 *要求* 已有一个有效的 MinIO Kubernetes Operator 安装，并假定本地主机也安装了与之匹配的 MinIO Kubernetes Operator。
本步骤默认使用最新稳定版 Operator，即版本 |operator-version-stable|。

有关部署 MinIO Operator 的完整文档，请参阅 :ref:`deploy-operator-kubernetes`。


可用 Worker Nodes
~~~~~~~~~~~~~~~~~

MinIO 会为新的 Tenant pool 部署额外的 :mc:`minio server <minio.server>` pod。
Kubernetes 集群 *必须* 具备足够的可用 worker node 来调度这些新 pod。

MinIO Operator 提供了用于控制 pod affinity 和 anti-affinity 的配置，以便将调度定向到特定 worker。

持久卷
~~~~~~

.. include:: /includes/common-admonitions.rst
   :start-after: start-exclusive-drive-access
   :end-before: end-exclusive-drive-access

MinIO 可以使用任何支持 :kube-docs:`ReadWriteOnce <concepts/storage/persistent-volumes/#access-modes>` 访问模式的 Kubernetes :kube-docs:`Persistent Volume (PV) <concepts/storage/persistent-volumes>`。
MinIO 的一致性保证依赖于 ``ReadWriteOnce`` 提供的独占存储访问。

对于节点具有 Direct Attached Storage 的 Kubernetes 集群，MinIO 强烈建议使用 `DirectPV CSI driver <https://min.io/directpv?ref=docs>`__。
DirectPV 提供了一个分布式持久卷管理器，可在 Kubernetes 节点之间发现、格式化、挂载、调度和监控驱动器。
DirectPV 解决了手动配置和监控 :kube-docs:`local persistent volumes <concepts/storage/volumes/#local>` 的局限性。

.. note::

   EKS 上的 MinIO Tenant 必须使用 :github:`EBS CSI Driver <kubernetes-sigs/aws-ebs-csi-driver>` 来预配所需的底层持久卷。
   MinIO 强烈建议使用基于 SSD 的 EBS 卷以获得最佳性能。
   有关 EBS 资源的更多信息，请参阅 `EBS Volume Types <https://aws.amazon.com/ebs/volume-types/>`__。

步骤
----

MinIO Operator 支持通过添加额外 pool 来扩展 MinIO Tenant。

.. tab-set::

   .. tab-item:: Kustomization

      #. 检查描述 Tenant 对象（``tenant.yaml``）的 Kustomization 对象。

         ``spec.pools`` 数组描述当前的 pool 拓扑。

      #. 在 ``spec.pools`` 数组中新增一个条目。

         新 pool 必须反映你期望的 Worker node、每个 server 的卷数量、存储类以及 affinity/scheduler 设置组合。
         有关与 Pool 相关配置项的更完整文档，请参阅 :ref:`minio-operator-crd`。

      #. 应用更新后的 Tenant 配置

         使用 ``kubectl apply`` 命令更新 Tenant：

         .. code-block:: shell

            kubectl apply -k ~/kustomization/TENANT-NAME

         请根据本地配置修改 Kustomization 目录路径。

   .. tab-item:: Helm

      #. 检查 Helm ``values.yaml`` 文件。

         ``tenant.pools`` 数组描述当前的 pool 拓扑。

      #. 在 ``tenant.pools`` 数组中新增一个条目。

         新 pool 必须反映你期望的 Worker node、每个 server 的卷数量、存储类以及 affinity/scheduler 设置组合。
         有关与 Pool 相关配置项的更完整文档，请参阅 :ref:`minio-tenant-chart-values`。

      #. 应用更新后的 Tenant 配置

         使用 ``helm upgrade`` 命令更新 Tenant：

         .. code-block:: shell

            helm upgrade TENANT-NAME minio-operator/tenant -f values.yaml -n TENANT-NAMESPACE

         上述命令默认使用的是 MinIO Operator Chart 仓库。
         如果你是手动安装 Chart，或使用了不同的仓库名称，请在命令中指定相应的 chart 或名称。

         分别将 ``TENANT-NAME`` 和 ``TENANT-NAMESPACE`` 替换为 Tenant 的名称和命名空间。
         你可以使用 ``helm list -n TENANT-NAMESPACE`` 验证 Tenant 名称。

你可以使用 ``kubectl get events -n TENANT-NAMESPACE --watch`` 监控扩容进度。
MinIO Operator 会更新 service，以便在新节点之间正确路由连接。
如果你使用了自定义 service、route、ingress 或类似 Kubernetes 网络组件，可能还需要针对新的 pod 主机名范围更新这些组件。
