.. _minio-restore-hardware-failure-site:

============
站点故障恢复
============

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 1

尽管整个站点丢失属于重大事故，MinIO 仍可将其影响控制在相对较小且可恢复的范围内。
站点恢复方式取决于该站点使用的复制方案。

.. list-table::
   :widths: 25 75
   :width: 100%

   * - Site Replication
     - 从健康对等站点完整恢复 IAM 配置、存储桶配置和数据
   * - Bucket Replication
     - 对每个已配置复制的存储桶，从健康远端位置恢复对象和元数据
   * - :mc:`mc mirror`
     - 仅从健康远端位置恢复对象数据，不包含版本控制信息

站点复制自愈会自动将现有站点中的 IAM 设置、存储桶、存储桶配置和对象添加到新站点，无需额外操作。

如果其他健康站点上仍保留任何存储桶复制规则，则无法配置站点复制。
存储桶复制与站点复制互斥。

如果你准备从存储桶复制切换到站点复制，则必须先在健康站点上移除所有存储桶复制规则，然后再配置站点复制。

将不健康对等站点恢复到 Site Replication
----------------------------------------

.. important::

   :minio-release:`RELEASE.2023-01-02T09-40-09Z` 版 MinIO server 包含重要修复，用于在包含三个或更多对等站点的复制配置中移除已下线站点。

   对于已配置站点复制的部署，请规划将所有对等站点 :ref:`测试并升级 <minio-upgrade>` 到该版本。
   一旦发生站点故障，你可以先将剩余健康站点更新到该指定版本，再执行本流程。

:ref:`站点复制 <minio-site-replication-overview>` 可让两个或更多 MinIO 部署在 IAM 策略、存储桶、存储桶配置、对象及对象元数据方面保持同步。
如果某个对等站点由于重大灾害或长期停电等原因失效，你可以使用剩余健康站点来恢复 :ref:`可复制数据 <minio-site-replication-what-replicates>`。

以下流程适用于站点丢失前已启用 :ref:`站点复制 <minio-site-replication-overview>` 的场景，并可用于恢复数据。
本流程假设一个或多个对等站点已 *完全丢失*，而不是由于复制滞后、网络延迟或部署短暂停机所导致的延后。

#. 使用带 ``--force`` 选项的 :mc-cmd:`mc admin replicate rm` 命令，将故障站点从 MinIO 站点复制配置中移除。

   以下命令会强制将一个不健康的对等站点从复制配置中移除：

   .. code-block:: shell
      :class: copyable

      mc admin replicate rm HEALTHY_PEER UNHEALTHY_PEER --force

   - 将 ``HEALTHY_PEER`` 替换为复制配置中任一健康对等站点的 :ref:`alias <alias>`

   - 将 ``UNHEALTHY_PEER`` 替换为不健康对等站点的 alias

   站点复制配置中的所有健康对等站点都会自动更新并移除该不健康对等站点。
   你可以使用 :mc-cmd:`mc admin replicate info` 命令验证新的站点复制配置。

#. 按照 :ref:`站点复制要求 <minio-expand-site-replication>` 部署一个新的 MinIO 站点。

   - 不要上传任何数据，也不要在既定要求之外对部署进行其他配置。
   - 验证新的 MinIO 部署运行正常，并且与其他对等站点具备双向连通性。
   - 确保新站点与现有对等站点使用相同的 server 版本

   .. warning::

      :mc-cmd:`mc admin replicate rm --force` 命令只会作用于站点复制配置中在线或健康的节点。
      被移除的离线 MinIO 部署仍会保留其原始复制配置，因此如果该部署恢复正常运行，它仍会继续向已配置的对等站点执行复制操作。

      如果你计划复用这些硬件重新加入站点复制配置，那么在重新初始化 MinIO 并将该站点重新加入复制配置之前，必须彻底清空该部署的驱动器。

#. 将 :ref:`替换后的对等站点加入 <minio-expand-site-replication>` 复制配置。

   使用 :mc-cmd:`mc admin replicate add` 命令，将新站点加入复制配置：

   .. code-block:: shell
      :class: copyable

      mc admin replicate add HEALTHY_PEER NEW_PEER

   - 将 ``HEALTHY_PEER`` 替换为复制配置中任一健康对等站点的 :ref:`alias <alias>`

   - 将 ``NEW_PEER`` 替换为新对等站点的 alias

   站点复制配置中的所有健康对等站点都会自动更新，将新对等站点纳入配置。
   你可以使用 :mc-cmd:`mc admin replicate info` 命令验证新的站点复制配置。

#. 使用 :mc-cmd:`mc admin replicate resync` 对新对等站点执行重同步。

   .. code-block:: shell
      :class: copyable

      mc admin replicate resync start HEALTHY_PEER NEW_PEER

   - 将 ``HEALTHY_PEER`` 替换为复制配置中任一健康对等站点的 :ref:`alias <alias>`

   - 将 ``NEW_PEER`` 替换为新对等站点的 alias


#. 验证复制状态。

   使用以下命令跟踪复制状态：

   - :mc-cmd:`mc admin replicate status` - 提供复制的整体状态和进度
   - :mc:`mc replicate status` - 提供存储桶级和全局复制状态

主动式存储桶复制重同步
----------------------

对于故障发生前已启用 :ref:`存储桶复制 <minio-bucket-replication>` 的场景，你可以使用 :mc:`mc replicate resync` 将数据恢复到新站点。
先创建一个新站点替换故障部署，然后将现有健康、且已启用存储桶复制的部署中的数据同步到新站点。

#. 部署一个新的 MinIO 站点。
#. 按需配置 IAM 和用户。
#. 在有数据的站点上，使用 :mc-cmd:`mc admin bucket remote add` 命令创建新的 ``remote target``，并记录输出中的 ARN。
#. 在有数据的站点上，使用上一步命令返回的 ARN 作为参数执行 :mc-cmd:`mc replicate resync start`，在新站点上重建存储桶。
#. 等待重同步完成（可使用 :mc-cmd:`mc replicate resync status` 检查）。
#. 从新的 MinIO 站点向现有目标存储桶配置存储桶复制规则。
#. `(Optional)` 删除目标部署中的存储桶复制规则，以恢复 active-passive 复制场景。

被动式存储桶复制重同步
----------------------

:ref:`存储桶复制 <minio-bucket-replication>` 可以通过将目标存储桶中的数据复制到新的 MinIO 站点，直接恢复站点内容。

作为被动过程，存储桶复制在站点恢复场景中可能无法达到理想的恢复速度。

存储桶复制依赖标准复制 :ref:`scanner <minio-concepts-scanner>` 队列，而该队列不会优先于其他过程执行。
如果恢复流程对 SLA/SLO 要求更严格，请按前文所述使用基于 :mc:`mc replicate resync` 命令的主动式存储桶复制流程。

存储桶复制规则会将对象、其 version ID、版本以及其他元数据复制到目标存储桶。
如果站点丢失前已启用存储桶复制，那么 MinIO 可以将带有这些属性的对象恢复到新的 MinIO 站点。

#. 部署一个新的 MinIO 站点。
#. 按需配置 IAM 和用户。
#. 在剩余的目标存储桶部署上，为每个存储桶创建指向新 MinIO 站点的存储桶复制规则。
#. 等待复制完成。
#. 从新的 MinIO 站点向现有目标存储桶配置存储桶复制规则。
#. `(Optional)` 删除目标部署中的存储桶复制规则，以恢复 active-passive 复制场景。

   如果你希望继续保持存储桶之间的 active-active 复制，请不要删除用于恢复数据的这些部署中的存储桶复制规则。
   在 active-active 复制中，任一位置上对象的变更都会影响另一位置上的对象。

镜像
----

MinIO 的镜像（mirroring）可以从任意兼容 S3 的存储系统复制对象。

无论源端如何，镜像（mirroring）只会复制每个对象的最新版本，不包含版本控制元数据。
因此你无法通过这种方式恢复这些属性。

当你只需要恢复对象的最新版本时，请使用 :mc:`mc mirror`。
如果你是从另一个 MinIO 部署复制数据，并希望恢复对象的版本历史及版本元数据，则应在这些机制原本已启用的前提下使用存储桶复制或站点复制。

#. 部署一个新的 MinIO 站点。
#. 按需配置 IAM 和用户。
#. 在新站点上创建存储桶。
#. 使用 :mc:`mc cp` CLI 命令，将镜像位置中的内容复制到新的 MinIO 站点。
