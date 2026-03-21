.. _minio-object-delete:

===============
对象删除
===============

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

概述
----

本页概述 ``DELETE`` 操作会如何影响对象，具体取决于包含该对象的存储桶配置。

以下因素的任意组合都可能影响 ``DELETE`` 操作的行为：

- :ref:`存储桶版本控制 <minio-bucket-versioning>`
- :ref:`对象锁定规则 <minio-object-locking>`
- :ref:`对象生命周期管理规则 <minio-lifecycle-management>`
- :ref:`对象分层 <minio-lifecycle-management-tiering>`
- :ref:`站点 <minio-site-replication-overview>` 或 :ref:`存储桶 <minio-replication-behavior-delete>` 复制
- :ref:`扫描器 <minio-concepts-scanner>`

权限
----

MinIO 使用 :ref:`基于策略的访问控制 <minio-policy>` 系统进行访问管理。
用户或服务账户必须提供正确的策略操作和条件，才能对该存储桶和对象执行 ``DELETE``。

未启用版本控制的对象
--------------------

如果对未启用版本控制的存储桶中的对象执行 ``DELETE`` 操作，其行为比较直接。
在确认用户或服务账户具有执行 ``DELETE`` 操作的权限后，MinIO 会永久删除该对象。

请求删除操作的用户或服务账户必须对该存储桶和对象具有 :policy-action:`s3:DeleteObject` 操作权限。

已启用版本控制的对象
--------------------

启用版本控制后，``DELETE`` 操作的行为会有所不同。

用户或服务账户必须对该存储桶和对象具有 :policy-action:`s3:DeleteObjectVersion` 操作权限。

删除当前版本
~~~~~~~~~~~~

如果对已启用版本控制的对象执行 ``DELETE`` 操作，但未指定版本 UUID，则会创建一个 ``DeleteMarker``，并将其置为该对象的 ``head``。

在这种情况下，MinIO 实际上不会从磁盘中删除该对象或其任何版本。
该对象的所有现有版本仍可通过指定对应版本的 UUID 进行访问。
当 ``DeleteMarker`` 成为该对象的 head 时，MinIO 不会为未指定版本 ID 的 ``GET`` 请求返回该对象。
相反，MinIO 会返回类似 ``404`` 的响应。

可以使用 :mc-cmd:`mc ls --versions` 查找对象版本的 UUID。

如需从驱动器中删除对象的当前版本，请先找到该版本的 UUID，然后使用 :mc-cmd:`mc rm --version-id=UUID ... <mc rm --version-id>` 删除当前版本。
在这种情况下，该对象紧邻的前一个版本将成为当前版本，并用于响应未指定 UUID 的该对象 ``GET`` 请求。

.. warning::

   在 DELETE 操作中指定 ``version-id`` 是不可逆的。
   MinIO 会从驱动器中删除指定版本，并且**无法**恢复。

删除先前版本
~~~~~~~~~~~~

如需删除对象的先前版本，请指定该版本的 UUID。
可以使用 :mc-cmd:`mc ls --versions` 获取版本 UUID。
当 ``DELETE`` 请求指定 ``version-id``，且用户具有删除该对象版本的正确权限时，MinIO 会从驱动器中永久删除指定版本。

.. warning::

   在 DELETE 操作中指定 ``version-id`` 是不可逆的。
   MinIO 会从驱动器中删除指定版本，并且**无法**恢复。

删除所有版本
~~~~~~~~~~~~

使用 :mc-cmd:`mc rm --versions` 删除对象的*所有*版本。
此操作不可逆。

生命周期管理过期
----------------

可以定义一个或多个 :ref:`生命周期管理过期规则 <minio-lifecycle-management-create-expiry-rule>`，使对象在达到指定的版本数量或经过指定时间后过期。
当版本数量超过规则指定值，或某个版本的时间早于指定阈值时，MinIO 会从驱动器中永久删除该对象版本。

这些规则依赖 :ref:`扫描器 <minio-concepts-scanner>` 在存储桶上处理规则。
扫描器以较低优先级持续运行，系统会优先处理 ``READ`` 和 ``WRITE`` 操作。
因此，满足过期条件的对象版本可能不会立即从 MinIO 中移除。

有关扫描器工作方式和配置选项的更多信息，请参阅 :ref:`扫描器 <minio-concepts-scanner>` 页面。

``DeleteMarkers`` 本身也是对象。
生命周期规则可以删除作为对象唯一剩余版本的 ``DeleteMarkers``。

.. versionchanged:: MinIO RELEASE.2024-05-01T01-11-10Z

使用 ``JSON`` 时，生命周期规则可以在指定天数后删除已删除对象的所有版本。

受保留规则保护的对象
--------------------

MinIO 会保护受 :ref:`锁定规则 <minio-object-locking>` 约束的对象，防止其被覆盖或删除。
这些规则要求对象在规则过期或被移除之前必须予以保留。

对已锁定对象执行未指定版本的 ``DELETE`` 操作，会为该对象创建一个 ``DeleteMarker``。
但对象各版本本身仍会按锁定要求予以保留。

指定对象版本的 ``DELETE`` 操作受保留规则约束。
对于受锁定保护的对象版本，MinIO 会在锁定过期或被移除之前防止其被覆盖或删除。

已复制对象
----------

复制会将对象从一个位置复制到另一个位置。
MinIO 支持存储桶级别或集群（“site”）级别的复制。

删除操作是否会被复制，取决于复制类型以及复制配置方式。

站点复制
~~~~~~~~

对于启用了 :ref:`多站点复制 <minio-site-replication-overview>` 的集群，MinIO 会将任一集群上执行的所有 ``delete`` 操作复制到对等组中的其他每个集群。

任一单个对等节点上的删除行为，都遵循普通 MinIO 部署相同的处理流程。

存储桶复制
~~~~~~~~~~

通过 :ref:`存储桶复制 <minio-bucket-replication>`，MinIO 支持在源存储桶与配置好的远程存储桶之间复制删除操作。
MinIO 会同步删除特定对象版本以及新的 :s3-docs:`delete markers <delete-marker-replication.html>`。
删除操作复制使用与其他所有复制操作相同的 :ref:`复制流程 <minio-replication-process>`。

MinIO 要求*显式启用*带版本删除和 delete marker 复制。
使用 :mc-cmd:`mc replicate add --replicate` 字段指定 ``delete``、``delete-marker`` 或两者，以分别启用带版本删除和 delete marker 复制。
如需同时启用两者，请使用逗号分隔这两个字符串：``delete,delete-marker``。

对于 delete marker 复制，MinIO 会在删除操作创建 delete marker 后启动复制流程。
MinIO 使用 ``X-Minio-Replication-DeleteMarker-Status`` 元数据字段跟踪 delete marker 的复制状态。
在 :ref:`active-active <minio-bucket-replication-serverside-twoway>` 复制配置中，如果两个集群并发为某个对象创建 delete marker，或者在复制事件同步前一个或两个集群处于宕机状态，MinIO 可能会产生重复的 delete marker。

对于复制特定对象版本的删除，MinIO 会将该对象版本标记为 ``PENDING``，直到复制完成。
一旦远程目标删除了该对象版本，MinIO 就会删除源端的该对象版本。
虽然此过程可确保版本删除近似同步，但它也可能导致在初始删除操作之后，列表操作仍返回该对象版本。
MinIO 使用 ``X-Minio-Replication-Delete-Status`` 跟踪删除版本的复制状态。

MinIO 只复制由客户端显式触发的删除操作。
MinIO *不会* 复制由 :ref:`生命周期管理过期规则 <minio-lifecycle-management-expiration>` 删除的对象。
对于 :ref:`active-active <minio-bucket-replication-serverside-twoway>` 配置，应在*所有*复制存储桶上设置相同的过期规则，以确保对象过期行为一致。

.. admonition:: MinIO 会清理源存储桶和远程存储桶中的空对象前缀
   :class: note, dropdown

   如果某次删除操作移除了某个存储桶前缀中的最后一个对象，MinIO 会递归删除该前缀中直到存储桶根为止的每一个空层级。
   MinIO 仅对作为对象写入操作一部分而*隐式*创建的前缀执行这种递归删除。
   对于使用显式目录创建命令（例如 :mc:`mc mb`）创建的前缀，MinIO 不会递归删除。

   如果复制规则启用了删除操作复制，则复制过程在目标 MinIO 集群上*同样*会应用这种隐式前缀清理行为。

   例如，考虑一个名为 ``photos`` 的存储桶，其中包含以下对象前缀：
   
   - ``photos/2021/january/myphoto.jpg`` // ``2021/january/`` 根据对象名隐式创建
   - ``photos/2021/february/myotherphoto.jpg``  // ``2021/february/`` 根据对象名隐式创建
   - ``photos/NYE21/NewYears.jpg``  // ``NYE21/`` 在存储桶中显式创建

   ``photos/NYE21`` 是*唯一*使用 :mc:`mc mb` 显式创建的前缀。
   其他所有前缀都是在写入位于该前缀下的对象时*隐式*创建的。
   
   - 某个命令删除了 ``myphoto.jpg``。
     MinIO 会自动清理空的 ``/january/`` 前缀。
   
   - 某个命令随后删除了 ``myotherphoto.jpg``。
     MinIO 会自动清理 ``/february/`` 前缀，以及此时已为空的 ``/2021`` 前缀。
   
   - 某个命令删除了 ``NewYears.jpg`` 对象。
     由于 ``/NYE21/`` 是*显式*创建的，MinIO 会保留该前缀。
