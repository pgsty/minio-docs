.. _minio-bucket-replication:

==========
存储桶复制
==========

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 1

MinIO 支持在源存储桶与目标存储桶之间进行服务端和客户端对象复制。

:ref:`Server-Side Bucket Replication <minio-bucket-replication-serverside>`
  为每个存储桶配置规则，以便在 MinIO 部署之间自动同步对象。
  配置存储桶复制规则的部署充当“源”，而配置的远端部署充当“目标”。
  MinIO 会在对象写入操作（例如 ``PUT``）期间应用规则，并自动同步新对象以及对象变更，例如新的对象版本或对象元数据的变更。
  
  MinIO 服务端存储桶复制仅支持将处于相同发行版本的 MinIO 集群作为远端复制目标。

客户端存储桶复制
  使用命令流程在同一 S3 兼容集群内的存储桶之间，或在两个相互独立的 S3 兼容集群之间同步对象。
  使用 :mc:`mc mirror` 的客户端复制支持 MinIO 到 S3 以及类似的复制配置。

.. admonition:: 存储桶复制与站点复制
   :class: note

   存储桶复制与 :ref:`站点复制 <minio-site-replication-overview>` 不同，且二者互斥。

   - 存储桶复制在存储桶级别同步数据，例如存储桶前缀路径和对象。

     你可以在任何时候配置存储桶复制，并且远端 MinIO 部署上的复制目标存储桶中可以预先存在数据。

   - 站点复制在存储桶复制的基础上扩展到包含 :ref:`IAM <minio-authentication-and-identity-management>`、安全令牌、访问密钥以及存储桶级配置。

     站点复制通常在最初部署 MinIO 对等站点时配置。
     在初始配置时，任意存储桶或对象只能由一个站点持有。


.. _minio-bucket-replication-serverside:

服务端存储桶复制
----------------

MinIO 服务端存储桶复制是一种自动化的存储桶级配置，用于在源存储桶和目标存储桶之间同步对象。
MinIO 服务端复制 *要求* 源存储桶和目标存储桶分别位于两个独立的 MinIO 集群中，且运行相同的 MinIO 服务端版本。

对于写入存储桶的每次操作，MinIO 都会检查该存储桶上配置的所有复制规则，并应用已配置优先级最高的匹配规则。
MinIO 会同步新对象以及对象变更，例如新的对象版本或对象元数据变更。
这也包括启用或修改对象锁定或保留设置等元数据操作。

MinIO 服务端存储桶复制在功能上类似于 Amazon S3 replication，同时增加了以下 MinIO 专有特性：

- 源存储桶和目标存储桶名称可以相同，从而支持 Splunk 或 Veeam BC/DR 等站点到站点用例。

- 相比 S3 存储桶复制配置，实现更简化，无需配置 AccessControlTranslation、Metrics 和 SourceSelectionCriteria 等设置。

- 支持源存储桶与目标存储桶之间对象的 Active-Active（双向）复制。

- 支持在三个或更多 MinIO 部署之间进行对象的多站点复制

.. _minio-replication-behavior-resync:

重新同步（灾难恢复）
~~~~~~~~~~~~~~~~~~~~

重新同步主要用于在副本配置中利用健康部署，在某个 MinIO 部署发生部分或全部数据丢失后进行恢复。
使用 :mc:`mc replicate resync` 命令，可基于指定的源存储桶对远端目标（:mc:`mc admin bucket remote`）执行完整重新同步。

重新同步过程会根据所有已配置且包含 :ref:`现有对象复制
<minio-replication-behavior-existing-objects>` 的复制规则检查源存储桶中的所有对象。
对于每个匹配规则的对象，重新同步过程都会将其放入复制
:ref:`队列 <minio-replication-process>`，而不考虑该对象当前的
:ref:`复制状态 <minio-replication-process>`。

对于远端副本与源对象完全一致（包括对象元数据）的对象，MinIO 会跳过同步。
除此之外，MinIO 不会基于目标端已有内容对队列进行优先级调整或修改。

:mc:`mc replicate resync` 在存储桶级别运行，*不* 支持前缀级粒度。
在大型存储桶上启动重新同步可能会显著增加与复制相关的负载和流量。
请谨慎使用此命令，并仅在必要时使用。

对于已配置 :ref:`对象转换（分层）
<minio-lifecycle-management-tiering>` 的存储桶，复制重新同步会以未转换状态恢复对象，且不包含任何关联的转换元数据。
因此，任何先前已转换到远端存储的数据都会与远端 MinIO 部署永久断开关联。
对于在远端配置中将明确的人类可读前缀作为一部分指定的分层配置，你可以安全地清除该前缀中的已转换数据，以避免与这些“丢失”数据相关的成本。

.. _minio-replication-behavior-delete:

删除操作的复制
~~~~~~~~~~~~~~

MinIO 支持复制 :ref:`删除 <minio-object-delete>` 操作，即同步删除特定对象版本以及新的
:s3-docs:`删除标记 <delete-marker-replication.html>`。删除操作复制使用与其他所有复制操作相同的
:ref:`复制流程 <minio-replication-process>`。

MinIO 要求显式启用带版本的删除和删除标记复制。
使用 :mc-cmd:`mc replicate add --replicate` 字段，通过指定 ``delete`` 和/或 ``delete-marker`` 来分别启用带版本的删除和删除标记复制。
若要同时启用两者，请使用逗号分隔符 ``delete,delete-marker`` 指定这两个字符串。

对于删除标记复制，MinIO 会在删除操作创建删除标记后启动复制流程。
MinIO 使用 ``X-Minio-Replication-DeleteMarker-Status`` 元数据字段来跟踪删除标记复制状态。
在 :ref:`active-active <minio-bucket-replication-serverside-twoway>` 复制配置中，如果两个集群同时为某个对象创建删除标记，或者在复制事件同步之前其中一个或两个集群处于宕机状态，MinIO 可能会产生重复的删除标记。

对于复制删除某个特定对象版本的操作，在复制完成之前，MinIO 会将该对象版本标记为 ``PENDING``。
一旦远端目标删除了该对象版本，MinIO 就会删除源端上的该对象。
虽然这一过程可确保接近同步的版本删除，但它可能导致在初始删除操作之后，列表操作仍返回该对象版本。
MinIO 使用 ``X-Minio-Replication-Delete-Status`` 跟踪删除版本的复制状态。

MinIO 仅复制由客户端显式发起的删除操作。
MinIO *不会* 复制因应用 :ref:`生命周期管理过期规则
<minio-lifecycle-management-expiration>` 而删除的对象。
对于 :ref:`active-active <minio-bucket-replication-serverside-twoway>` 配置，请在 *所有* 复制存储桶上设置相同的过期规则，以确保对象过期行为一致。

.. admonition:: MinIO 会在源存储桶和远端存储桶上裁剪空对象前缀
   :class: note, dropdown

   如果删除操作移除了某个存储桶前缀中的最后一个对象，MinIO 会递归删除该前缀中所有为空的部分，直到存储桶根目录。
   MinIO 仅对在对象写入过程中 *隐式* 创建的前缀应用这种递归删除行为，也就是说，该前缀不是通过 :mc:`mc mb` 之类的显式目录创建命令创建的。

   如果复制规则启用了删除操作复制，那么复制过程在目标 MinIO 集群上 *也会* 应用这种隐式前缀裁剪行为。

   例如，考虑一个名为 ``photos`` 的存储桶，其中包含以下对象前缀：
   
   - ``photos/2021/january/myphoto.jpg``
   - ``photos/2021/february/myotherphoto.jpg``
   - ``photos/NYE21/NewYears.jpg``

   ``photos/NYE21`` 是 *唯一一个* 使用 :mc:`mc mb` 显式创建的前缀。
   其他所有前缀都是在写入位于该前缀下的对象时 *隐式* 创建的。
   
   - 某个命令删除了 ``myphoto.jpg``。MinIO 会自动裁剪空的 ``/janaury`` 前缀。
   
   - 随后某个命令删除了 ``myotherphoto.jpg``。MinIO 会自动裁剪 ``/february`` 前缀以及此时已为空的 ``/2021`` 前缀。
   
   - 某个命令删除了 ``NewYears.jpg`` 对象。MinIO 会保留 ``/NYE21`` 前缀，因为它是 *显式* 创建的。

.. _minio-replication-behavior-existing-objects:

现有对象的复制
~~~~~~~~~~~~~~

默认情况下，MinIO 会将源存储桶中的现有对象复制到配置好的远端，类似于 `AWS: Replicating existing objects between S3 buckets <https://aws.amazon.com/blogs/storage/replicating-existing-objects-between-s3-buckets/>`__，但无需联系技术支持的额外成本。

MinIO 会将所有满足复制规则的对象或对象前缀标记为可同步到远端集群和存储桶。
MinIO 仅排除没有版本 ID 的对象，例如在存储桶启用版本控制之前写入的对象。

你可以在配置或修改存储桶复制规则时禁用现有对象复制。
在创建或修改时，必须指定 *所有* 需要的复制特性：

- 对于新的复制规则，不要将 ``"existing-objects"`` 包含在 :mc-cmd:`mc replicate add --replicate` 指定的复制特性列表中。

- 对于现有复制规则，使用 :mc-cmd:`mc replicate update --replicate` 从现有复制特性列表中移除 ``"existing-objects"``。
  新规则会 **替换** 旧规则。

禁用现有对象复制不会移除任何已经复制到远端存储桶的对象。


同步复制与异步复制
~~~~~~~~~~~~~~~~~~

.. include:: /includes/common-replication.rst
   :start-after: start-replication-sync-vs-async
   :end-before: end-replication-sync-vs-async

在使用 :mc-cmd:`mc admin bucket remote add` 命令配置远端目标时，你必须通过
:mc-cmd:`~mc admin bucket remote add` 标志显式启用同步复制。

复制内部机制
~~~~~~~~~~~~

本节记录复制的内部行为，对使用或实现复制而言并非关键。
本文档仅用于学习和教育目的。

.. _minio-replication-process:

复制流程
++++++++

MinIO 使用一个复制排队系统，并由多个并发复制工作线程处理该队列。
MinIO 会持续执行复制并从队列中移除对象，同时扫描新的未复制对象并将其加入队列。


.. versionchanged:: RELEASE.2022-07-18T17-49-40Z

   MinIO 会将失败的复制操作加入队列，并最多重试三（3）次。
   
   对于在三次尝试后仍复制失败的操作，MinIO 会将其移出队列。
   扫描器稍后可以再次发现这些受影响对象，并将其重新加入复制队列。
  
.. versionchanged:: RELEASE.2022-08-11T04-37-28Z

   在执行列表操作或任何 ``GET``、``HEAD`` API 方法时，失败或待处理的复制会自动重新入队。
   例如，在远端位置恢复在线后，使用 :mc:`mc stat`、:mc:`mc cat` 或 :mc:`mc ls` 会使复制重新入队。

MinIO 会根据对象的复制状态设置 ``X-Amz-Replication-Status`` 元数据字段：

.. list-table::
   :header-rows: 1
   :width: 100%
   :widths: 30 70

   * - 复制状态
     - 说明

   * - ``PENDING``
     - 该对象尚未被复制。如果对象满足该存储桶上配置的某条复制规则，MinIO 就会应用此状态。
       MinIO 会持续扫描尚未进入复制队列的 ``PENDING`` 对象，并在队列有可用空间时将其加入队列。

       对于多站点复制，对象会一直保持 ``PENDING`` 状态，直到复制到该存储桶或存储桶前缀配置的 *所有* 远端。

   * - ``COMPLETED``
     - 该对象已成功复制到远端集群。

   * - ``FAILED``
     - 该对象复制到远端集群失败。

       MinIO 会持续扫描尚未进入复制队列的 ``FAILED`` 对象，并在队列有可用空间时将其加入队列。

   * - ``REPLICA``
     - 该对象本身就是来自远端源的副本。

复制流程通常具有以下几种流转路径之一：

- ``PENDING -> COMPLETED``
- ``PENDING -> FAILED -> COMPLETED``

.. toctree::
   :hidden:
   :titlesonly:

   /administration/bucket-replication/bucket-replication-requirements
   /administration/bucket-replication/enable-server-side-one-way-bucket-replication
   /administration/bucket-replication/enable-server-side-two-way-bucket-replication
   /administration/bucket-replication/enable-server-side-multi-site-bucket-replication
   /administration/bucket-replication/server-side-replication-resynchronize-remote   
