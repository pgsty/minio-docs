.. _minio-restore-hardware-failure-drive:

==============
驱动器故障恢复
==============

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 1

MinIO 支持将故障驱动器热替换为新的健康驱动器。
MinIO 会检测这些驱动器并对其执行自愈，无需在节点级或部署级执行重启。
:ref:`MinIO 自愈 <minio-concepts-healing>` 仅发生在被替换的驱动器上，大多数情况下对部署性能影响很小或几乎可以忽略。

MinIO 自愈会确保恢复到驱动器上的所有数据保持一致且正确。

.. include:: /includes/common-admonitions.rst
   :start-after: start-exclusive-drive-access
   :end-before: end-exclusive-drive-access

以下步骤提供了更详细的驱动器替换流程。
这些步骤假定你使用的是一个 MinIO 部署，其中每个节点都按照 :ref:`文档中的前置条件 <minio-installation>`，通过 ``/etc/fstab`` 配合逐盘标签来管理驱动器。

1) 卸载故障驱动器
------------------------------

使用 ``umount`` 卸载每块故障驱动器。例如，以下命令会卸载位于 ``/dev/sdb`` 的驱动器：

.. code-block:: shell

   umount /dev/sdb

2) 替换故障驱动器
------------------------------

从节点硬件中移除故障驱动器，并将其替换为已知健康的驱动器。替换驱动器 *必须* 满足以下要求：

- 已按 :ref:`XFS 格式化 <minio-hardware-checklist-storage>` 且为空。
- 相同的驱动器类型（例如 HDD、SSD、NVMe）。
- 性能相同或更高。
- 容量相同或更大。

使用容量更大的替换驱动器并不会增加集群总存储量。
MinIO 会以 :ref:`服务器池 <minio-intro-server-pool>` 中 *最小* 驱动器的容量，作为该 服务器池 内所有驱动器的上限。

以下命令会将驱动器格式化为 XFS，并为其分配一个与故障驱动器一致的标签。

.. code-block:: shell

   mkfs.xfs /dev/sdb -L DRIVE1

MinIO **强烈建议** 使用基于标签的挂载方式，以确保驱动器顺序在系统重启后仍保持一致。

3) 审查并更新 ``fstab``
-------------------------------

检查 ``/etc/fstab`` 文件，并按需更新，使故障驱动器对应条目指向新格式化的替换盘。

- 如果使用基于标签的驱动器分配方式，请确保每个标签都指向正确的新格式化驱动器。

- 如果使用基于 UUID 的驱动器分配方式，请根据新格式化驱动器更新每个挂载点对应的 UUID。你可以使用 ``lsblk`` 查看驱动器 UUID。

例如，考虑以下配置：

.. code-block:: shell

   $ cat /etc/fstab

     # <file system>  <mount point>  <type>  <options>         <dump>  <pass>
     LABEL=DRIVE1     /mnt/drive1    xfs     defaults,noatime  0       2
     LABEL=DRIVE2     /mnt/drive2    xfs     defaults,noatime  0       2
     LABEL=DRIVE3     /mnt/drive3    xfs     defaults,noatime  0       2
     LABEL=DRIVE4     /mnt/drive4    xfs     defaults,noatime  0       2

.. note::

   依赖挂载外部存储的云环境实例，如果一个或多个远程文件挂载返回错误或失败，可能会遇到启动失败。
   例如，挂载持久化 EBS 卷的 AWS ECS 实例，如果一个或多个 EBS 卷挂载失败，可能无法按标准 ``/etc/fstab`` 配置正常启动。

   你可以设置 ``nofail`` 选项，在启动时静默这些错误，并允许实例在存在一个或多个挂载问题时继续启动。

   但在使用本地直连磁盘的系统上，不应使用该选项，因为静默驱动器错误会阻止 MinIO 和操作系统以正常方式响应这些错误。

基于前述示例命令，由于 ``/mnt/drive1`` 处的替换驱动器与故障驱动器使用相同的 ``DRIVE1`` 标签，因此 ``fstab`` 无需修改。

4) 重新挂载替换后的驱动器
--------------------------------

使用 ``mount -a`` 重新挂载本流程开始时卸载的驱动器：

.. code-block:: shell
   :class: copyable

   mount -a

该命令应完成对所有替换驱动器的重新挂载。

5) 监控 MinIO 的驱动器识别与自愈状态
--------------------------------------------------

重新挂载驱动器后，使用 :mc:`mc admin logs` 命令 *或* 在 ``systemd`` 管理的安装中使用 ``journalctl -u minio``，监控服务端日志输出。
输出中应包含识别到每块已格式化且为空驱动器的消息。

使用 :mc:`mc admin heal` 监控部署整体的 :ref:`自愈 <minio-concepts-healing>` 状态。
MinIO 会积极地对替换驱动器执行自愈，以确保部署快速从降级状态恢复。

6) 后续步骤
-----------

继续监控集群中是否出现更多驱动器故障。某些批次的驱动器可能会在接近的时间窗口内集中失效。
若部署中的驱动器故障率高于预期，应安排专项维护，集中替换已知存在问题的驱动器批次。
可考虑使用 `MinIO SUBNET <https://min.io/pricing?jmp=docs>`__，与 MinIO Engineering 协作获取此类操作的指导。
