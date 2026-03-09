.. Used in the following pages:
   - /monitoring/bucket-notifications/publish-events-to-amqp.rst

.. Used in the following pages:
   /reference/minio-cli/minio-mc/mc-rm.rst
   /reference/minio-cli/minio-mc/mc-mv.rst
   /reference/minio-cli/minio-mc/mc-mirror.rst

.. start-remove-api-trims-prefixes

|command| 依赖 :mc:`mc` 的删除 API 来移除对象。在删除存储桶前缀中的最后一个对象时，
:mc:`mc` 还会递归删除直到存储桶根为止的每一个空前缀。:mc:`mc` 只会对在对象写入
操作过程中 *隐式* 创建的前缀执行这种递归删除，也就是说，该前缀不是通过
:mc:`mc mb` 这类显式目录创建命令创建的。

例如，假设存储桶 ``photos`` 中有以下对象前缀：

- ``photos/2021/january/myphoto.jpg``
- ``photos/2021/february/myotherphoto.jpg``
- ``photos/NYE21/NewYears.jpg``

``photos/NYE21`` 是 *唯一* 通过 :mc:`mc mb` 显式创建的前缀。其余所有前缀
都是在向该前缀写入对象时 *隐式* 创建的。

如果某个 :mc:`mc` 命令删除了 ``myphoto.jpg``，删除 API 会自动裁剪空的
``/january`` 前缀。如果后续某个 :mc:`mc` 命令又删除了 ``myotherphoto.jpg``，
删除 API 会自动裁剪 ``/february`` 前缀，以及此时已经为空的 ``/2021`` 前缀。
如果某个 :mc:`mc` 命令删除了 ``NewYears.jpg``，由于 ``/NYE21`` 是
*显式* 创建的，因此该前缀会保留不变。

.. end-remove-api-trims-prefixes

.. Following is linked topically to the remove-api-trims-prefixes core

.. start-remove-api-trims-prefixes-fs

如果将 |command| 用于文件系统操作，:mc:`mc` 也会采用相同行为，递归裁剪直到
根目录的所有空目录。不过，:mc:`mc` 的删除 API 无法区分某个目录路径是显式
创建的，还是隐式创建的。如果 |command| 删除了某个文件系统路径上的最后一个对象，
:mc:`mc` 会在删除过程中递归删除从该路径向上直到根目录的所有空目录。

.. end-remove-api-trims-prefixes-fs

.. The following exclusive access admonition is used on a number of pages:
   - administration/object-management.rst
   - administration/concepts.rst
   - operations/concepts.rst
   - operations/data-recovery.rst
   - operations/checklists/hardware.rst
   - operations/checklists/software.rst
   - operations/concepts/availability-and-resiliency.rst
   - operations/concepts/erasure-coding.rst
   - operations/data-recover/recover-after-drive-failure.rst
   - operations/data-recover/recover-after-node-failure.rst
   - operations/install-deploy-manage/deploy-minio-multi-node-multi-drive.rst
   - operations/install-deploy-manage/deploy-minio-single-node-multi-drive.rst
   - operations/install-deploy-manage/deploy-minio-single-node-single-drive.rst
   - operations/install-deploy-manage/deploy-minio-tenant.rst
   - operations/install-deploy-manage/expand-minio-deployment.rst
   - operations/install-deploy-manage/expand-minio-tenant.rst
   - glossary.rst

.. start-exclusive-drive-access

.. admonition:: 磁盘独占访问
   :class: warning

   MinIO **要求** 对用于对象存储的磁盘或卷拥有 *独占* 访问权限。
   任何其他进程、软件、脚本或人员都不应直接对提供给 MinIO 的磁盘或卷，
   或 MinIO 在其上放置的对象或文件执行 *任何* 操作。
   
   除非得到 MinIO Engineering 的明确指示，否则不要使用脚本或工具直接修改、
   删除或移动这些磁盘上的任何数据分片、校验分片或元数据文件，包括在磁盘或节点
   之间迁移这些文件。
   这类操作极有可能导致大范围损坏和数据丢失，超出 MinIO 的自愈能力。

.. end-exclusive-drive-access
