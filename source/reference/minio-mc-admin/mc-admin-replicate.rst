.. _minio-mc-admin-replicate:

======================
``mc admin replicate``
======================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc admin replicate

.. versionchanged:: RELEASE.2023-01-11T03-14-16Z

   - ``mc admin replicate edit`` renamed to :mc-cmd:`mc admin replicate update`
   - ``mc admin replicate remove`` renamed to :mc-cmd:`mc admin replicate rm`

描述
-----------

.. start-mc-admin-replicate-desc

:mc-cmd:`mc admin replicate` 命令用于为一组 MinIO 对等站点创建并管理 :ref:`站点复制 <minio-site-replication-overview>`。

站点复制类似于 active-active 存储桶复制，但适用于多个 MinIO 部署。
在这组站点中，无论 IAM 设置、存储桶或对象发生何种变更，该变更都会在站点复制组中的所有站点间复制。

.. end-mc-admin-replicate-desc

:ref:`存储桶复制 <minio-bucket-replication>` 用于在单个部署内或跨部署将特定存储桶或对象从一个位置镜像到另一个位置，而站点复制会持续将整个 MinIO 站点镜像到其他站点。

在配置站点复制时，:mc-cmd:`mc admin replicate` 仅支持 :ref:`分布式部署 <deploy-minio-distributed>` 的站点复制。

在初始化新的站点复制配置时，只允许一个部署包含数据。

站点复制会对所有存储桶强制启用 :ref:`存储桶版本控制 <minio-bucket-versioning>`，包括现有存储桶以及启动站点复制后新增的任何存储桶。
与仅处理对象最新版本的 :mc:`mc mirror` 相比，站点复制会完整同步版本化对象。

.. admonition:: 仅在 MinIO 部署上使用 ``mc admin``
   :class: note

   .. include:: /includes/facts-mc-admin.rst
      :start-after: start-minio-only
      :end-before: end-minio-only


:mc-cmd:`mc admin replicate` 命令包含以下子命令：

.. list-table::
   :header-rows: 1
   :widths: 40 60

   * - 子命令
     - 描述

   * - :mc-cmd:`mc admin replicate add`
     - 创建新的站点复制配置，或扩展现有配置。

   * - :mc-cmd:`mc admin replicate info`
     - 返回站点复制配置信息。

   * - :mc-cmd:`mc admin replicate resync`
     - 当第二个站点丢失数据时，将一个站点中的内容重新同步到第二个站点。

   * - :mc-cmd:`mc admin replicate rm`
     - 删除整个站点复制配置，或将一个或多个对等站点从站点复制中移除。

   * - :mc-cmd:`mc admin replicate status`
     - 显示参与站点之间 :ref:`可复制数据 <minio-site-replication-what-replicates>` 的状态。

   * - :mc-cmd:`mc admin replicate update`
     - 修改站点复制配置中指定对等站点的 endpoint。

语法
------

.. mc-cmd:: add
   :fullpath:

   创建或扩展站点复制配置。
   按 MinIO 的建议，该配置默认使用异步站点复制。

   若要启用同步站点复制，请先使用此命令创建复制配置。
   然后使用 :mc-cmd:`mc admin replicate update --mode sync <mc admin replicate update --mode>` 更新配置。

   .. tab-set::

      .. tab-item:: 示例

         假设一个多站点 MinIO 拓扑包含三个独立的 MinIO 部署，使用以下 :ref:`别名 <alias>`：``minio1``、``minio2`` 和 ``minio3``。
         三个站点之间都具备完整的双向网络访问，并且站点间延迟较低。

         .. code-block:: shell
            :class: copyable

            mc admin replicate add minio1 minio2 minio3

         以下命令将一个现有站点复制配置（包含对等站点 ``minio1``、``minio2``、``minio3`` 和 ``minio4``）扩展到新的对等站点 ``minio5``。
         ``minio5`` 不包含任何数据。
         请先列出 *所有* 现有对等站点。
         最后列出要扩展到的站点。

         如果任一现有站点不可达，请先使用 :mc-cmd:`mc admin replicate rm` 删除不可达站点，再继续扩展站点复制。

         .. code-block:: shell
            :class: copyable

            mc admin replicate add minio1 minio2 minio3 minio4 minio5

         以下命令创建新的站点复制配置，并在对等站点 ``minio1``、``minio2`` 和 ``minio3`` 之间同步 ILM 过期规则。

         .. code-block:: shell
            :class: copyable

            mc admin replicate add minio1 minio2 minio3 --replicate-ilm-expiry

      .. tab-item:: 语法

         命令语法如下：

         .. code-block:: shell
            :class: copyable

            mc [GLOBALFLAGS] admin replicate add      \
                                        ALIAS1        \
                                        ALIAS2        \
                                        [ALIAS3 ...]  \
                                        [--replicate-ilm-expiry]

   .. mc-cmd:: ALIAS
      :required:

      要纳入站点复制的 MinIO 部署 :ref:`别名 <alias>`。

      创建站点复制至少需要两个 MinIO 部署别名。
      只有第一个别名可以包含存储桶或对象。
      第一个站点也可以为空。

      要将现有站点复制扩展到一个或多个新站点，请先列出待扩展的站点复制集合中所有现有对等站点的 :ref:`别名 <alias>`。
      然后再附加一个或多个 :ref:`别名 <alias>`，将其加入现有站点复制。
      新增对等站点必须为空。

   .. mc-cmd:: --replicate-ilm-expiry
      :optional:

      .. versionadded:: mc RELEASE.2023-12-02T02-03-28Z

      在对等站点间复制 :ref:`ILM expiration <minio-lifecycle-management-expiration>` 规则。

.. mc-cmd:: update
   :fullpath:

   修改参与站点复制的现有对等站点所使用的 endpoint。

   .. versionchanged:: RELEASE.2023-01-11T03-14-16Z

      ``mc admin replicate edit`` renamed to ``mc admin replicate update``.

   .. tab-set::

      .. tab-item:: 示例

         .. code-block:: shell
            :class: copyable

            mc admin replicate update                                                   \
                               minio2                                                 \
                               --deployment-id c1758167-4426-454f-9aae-5c3dfdf6df64   \
                               --endpoint https://minio2:9000

      .. tab-item:: 语法

         命令语法如下：

         .. code-block:: shell

            mc [GLOBALFLAGS] admin replicate update                     \
                                        ALIAS                           \
                                        --deployment-id [deploymentID]  \
                                        --endpoint [newEndpoint]        \
                                        --mode ["sync" | "async"]       \
                                        --enable-ilm-expiry-replication \
                                        --disable-ilm-expiry-replication

   .. mc-cmd:: ALIAS
      :required:

      MinIO 部署的 :ref:`别名 <alias>`。

   .. mc-cmd:: --bucket-bandwidth

      以每秒比特为单位设置存储桶的默认带宽限制。

      有效单位包括：

      - ``B`` 表示字节
      - ``K`` 表示千字节
      - ``M`` 表示兆字节
      - ``G`` 表示吉字节
      - ``T`` 表示太字节
      - ``Ki`` 表示 kibibyte
      - ``Mi`` 表示 mibibyte
      - ``Gi`` 表示 gibibyte
      - ``Ti`` 表示 tebibyte

      例如，以下命令将 ``myminio`` 部署上的复制带宽限制为不超过每秒 2 Gigabytes。

      .. code-block:: shell
         :class: copyable

         mc admin replicate update myminio --deployment-id c1758167-4426-454f-9aae-5c3dfdf6df64 --bucket-bandwidth "2G"

   .. mc-cmd:: --deployment-id
      :required:

      要修改的部署唯一 ID。

      可通过运行 :mc-cmd:`mc admin replicate info ALIAS` 获取部署 ID

   .. mc-cmd:: --disable-ilm-expiry-replication
      :optional:

      .. versionadded:: mc RELEASE.2023-12-02T02-03-28Z

      停止在对等站点之间复制 ILM 过期规则。
      对等站点之间已经同步的现有规则不会从任何对等站点移除。

   .. mc-cmd:: --enable-ilm-expiry-replication
      :optional:

      .. versionadded:: mc RELEASE.2023-12-02T02-03-28Z

      开始在对等站点之间复制 ILM 过期规则。

   .. mc-cmd:: --endpoint
      :required:

      与该对等站点关联的新 endpoint 或 URL。

   .. mc-cmd:: --mode
      :optional:

      指定 MinIO 对该对等站点执行同步或异步复制操作。
      可用值为 ``sync`` 和 ``async``。

      默认值为 ``async``。

   .. mc-cmd:: --sync
      :optional:

      .. important::

         ``--sync`` 标志自 ``RELEASE.2023-07-07T05-25-51Z`` 起已弃用。
         请改用 :mc-cmd:`~mc admin replicate update --mode`。

      启用或禁用同步站点复制。
      可用值为 ``enable`` 和 ``disable``。
      若未定义，MinIO 使用异步站点复制。

.. mc-cmd:: rm, remove
   :fullpath:

   .. versionchanged:: RELEASE.2023-01-11T03-14-16Z

      ``mc admin replicate remove`` 子命令重命名为 ``mc admin replicate rm``。

   从站点复制配置中移除一个或多个站点。

   请注意，如果你打算未来将该站点重新加入站点复制配置，则其必须不包含任何 :ref:`可复制数据 <minio-site-replication-what-replicates>`。

   .. tab-set::

      .. tab-item:: 示例

         从包含 `minio2` 的现有站点复制配置中，移除所有已连接站点的站点复制。
         这会删除所有参与站点的站点复制配置。

         .. code-block:: shell
            :class: copyable

            mc admin replicate rm      \
                               minio2  \
                               --all   \
                               --force

         从包含 `minio2` 的现有站点复制配置中移除别名为 ``minio5`` 和 ``minio6`` 的站点

         .. code-block:: shell
            :class: copyable

            mc admin replicate rm      \
                               minio2  \
                               minio5  \
                               minio6  \
                               --force

      .. tab-item:: 语法

         命令语法如下：

         .. code-block:: shell

            mc [GLOBALFLAGS] admin rm          \
                                   TARGET      \
                                   ALIAS1      \
                                   [ALIAS2...] \
                                   --all       \
                                   --force

   .. mc-cmd:: TARGET
      :required:

      要操作的站点复制中，处于活动状态的 MinIO 部署 :ref:`别名 <alias>`。
      除非要从站点复制中移除所有站点，否则不要使用待移除部署的别名。

   .. mc-cmd:: ALIAS
      :optional:

      要从站点复制配置中移除的活动 MinIO 部署 :ref:`别名 <alias>`。
      可重复指定以移除更多站点。

   .. mc-cmd:: --all
      :optional:

      包含此标志可移除为站点复制配置的所有站点，并结束该站点复制配置。

   .. mc-cmd:: --force
      :required:

      此标志会强制从站点复制配置中移除指定的对等站点。


.. mc-cmd:: info
   :fullpath:

   返回站点复制配置中各站点的信息。

   .. tab-set::

      .. tab-item:: 示例

         .. code-block:: shell
            :class: copyable

            mc admin replicate info minio1

      .. tab-item:: 语法

         .. code-block:: shell

            mc [GLOBALFLAGS] admin replicate info ALIAS

   .. mc-cmd:: ALIAS
      :required:

      站点复制配置中活动 MinIO 部署的 :ref:`别名 <alias>`。


.. mc-cmd:: status
   :fullpath:

   显示站点复制配置中站点、存储桶、用户、组或策略的状态。

   .. tab-set::

      .. tab-item:: 示例

         显示包含站点 ``minio1`` 的站点复制配置的整体复制状态。

         .. code-block:: shell

            mc admin replicate status minio1

         显示包含站点 ``minio1`` 的站点复制配置中，跨站点的存储桶复制状态。

         .. code-block:: shell

            mc admin replicate status     \
                               minio1     \
                               --buckets

         显示包含站点 ``minio1`` 的站点复制配置中，名为 ``images`` 的存储桶在跨站点间的站点复制状态。

         .. code-block:: shell

            mc admin replicate status           \
                                minio1          \
                                --bucket images

         显示包含站点 ``minio1`` 的站点复制配置中，用户 ``janedoe`` 设置在跨站点间的站点复制状态。

         .. code-block:: shell

            mc admin replicate status         \
                               minio1         \
                               --user janedoe

         上述示例的输出类似如下：

         .. code-block:: shell

            Bucket replication status:
            ●  30/30 Buckets in sync

            Policy replication status:
            ●  5/5 Policies in sync

            User replication status:
            ●  3/3 Users in sync

            Group replication status:
            No Groups present

            ILM Expiry Rules replication status:
            ●  5/5 ILM Expiry Rules in sync

            Object replication status:
            Replication status since 1 day
            Summary:
            Replicated:    0 objects (0 B)
            Queued:        - 0 objects, (0 B) (avg: 0 objects, 0 B; max: 0 objects, 0 B)
            Received:      0 objects (0 B)

         显示包含站点 ``minio1`` 的站点复制配置中，规则 ID 为 ``ckok9v5b4dtgofkbi6tg`` 的 ILM 过期规则在跨站点间的站点复制状态。

         .. code-block:: shell

            mc admin replicate status minio1 --ilm-expiry-rule ckok9v5b4dtgofkbi6tg

         输出类似如下：

         .. code-block:: shell

            ●  ILM Expiry Rule replication summary for: ckok9v5b4dtgofkbi6tg

            ILMExpiryRule   | MINIO1          | MINIO2
            ILM Expiry Rule | ✔               | ✔

      .. tab-item:: 语法

         .. code-block:: shell

            mc [GLOBALFLAGS] admin replicate status          \
                               TARGET                        \
                               [--all]                       \
                               [--buckets]                   \
                               [--bucket nameOfBucket]       \
                               [--groups]                    \
                               [--group nameOfGroup]         \
                               [--ilm-expiry-rules]          \
                               [--ilm-expiry-rule <rule ID>] \
                               [--policies]                  \
                               [--policy nameOfPolicy]       \
                               [--users]                     \
                               [--user accessKey]

   .. mc-cmd:: TARGET
      :required:

      站点复制配置中活动 MinIO 部署的 :ref:`别名 <alias>`。

   .. mc-cmd:: --all
      :optional:

      显示所有可用的站点复制状态信息。

   .. mc-cmd:: --buckets
      :optional:

      显示所有存储桶的复制状态。

   .. mc-cmd:: --bucket
      :optional:

      在该标志后指定存储桶名称，以显示特定存储桶的复制状态。

   .. mc-cmd:: --groups
      :optional:

      显示所有组的复制状态。

   .. mc-cmd:: --group
      :optional:

      在该标志后指定组名称，以显示特定组的复制状态。

   .. mc-cmd:: --ilm-expiry-rules
      :optional:

      .. versionadded:: mc RELEASE.2023-12-02T02-03-28Z

      显示 ILM 过期规则的同步信息。

      与 :mc-cmd:`~mc admin replicate status --ilm-expiry-rule` 互斥

   .. mc-cmd:: --ilm-expiry-rule
      :optional:

      .. versionadded:: mc RELEASE.2023-12-02T02-03-28Z

      显示指定 ILM 过期规则的复制状态信息。

      与 :mc-cmd:`~mc admin replicate status --ilm-expiry-rules` 互斥

   .. mc-cmd:: --policies
      :optional:

      显示所有策略的复制状态。

   .. mc-cmd:: --policy
      :optional:

      在该标志后指定策略名称，以显示特定策略的复制状态。

   .. mc-cmd:: --users
      :optional:

      显示所有用户的复制状态。

   .. mc-cmd:: --user
      :optional:

      在该标志后指定用户名，以显示特定用户的复制状态。

.. mc-cmd:: resync
   :fullpath:

   在数据丢失场景下，将复制配置中一个站点的数据重新同步到复制配置中的第二个站点。

   .. tab-set::

      .. tab-item:: 示例

         以下命令启动重新同步过程，将 ``minio1`` 的数据恢复到 ``minio2``

         .. code-block:: shell
            :class: copyable

            mc admin replicate resync start minio1 minio2

         以下命令显示当前进行中的重新同步状态。

         .. code-block:: shell
            :class: copyable

            mc admin replicate resync status minio1 minio2

         以下命令停止进行中的重新同步。

         .. code-block:: shell
            :class: copyable

            mc admin replicate resync cancel minio1 minio2

      .. tab-item:: 语法

         .. code-block:: shell

            mc [GLOBALFLAGS] admin replicate resync start|status|cancel ALIAS1 ALIAS2

         - 将 ``ALIAS1`` 替换为拥有待恢复数据的站点别名。
         - 将 ``ALIAS2`` 替换为需要重新同步数据的站点别名。

   .. mc-cmd:: start

      从拥有数据的一个站点到需要同步的第二个站点，发起新的重新同步流程。

   .. mc-cmd:: status

      显示已配置站点复制的两个站点之间，现有重新同步流程的状态。

   .. mc-cmd:: cancel

      结束已配置站点复制的两个站点之间当前进行中的重新同步流程。

   .. mc-cmd:: alias1

      站点复制配置中活动 MinIO 部署的 :ref:`别名 <alias>`，其包含你希望重新同步到另一个站点的数据。

   .. mc-cmd:: alias2

      站点复制配置中活动 MinIO 部署的 :ref:`别名 <alias>`，其需要从另一个站点重新同步数据。


全局标志
------------

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals
