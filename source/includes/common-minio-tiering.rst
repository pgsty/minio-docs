.. start-create-transition-rule-desc

使用 :mc:`mc ilm rule add` 命令为存储桶创建新的转移规则。
以下示例将对象配置为在指定的日历天数后执行转移：

.. code-block:: shell
   :class: copyable

   mc ilm rule add ALIAS/BUCKET \
   --transition-tier TIERNAME \
   --transition-days DAYS \
   --noncurrent-transition-days NONCURRENT_DAYS
   --noncurrent-transition-tier TIERNAME

上述示例指定了以下参数：

.. list-table::
   :header-rows: 1
   :widths: 30 70
   :width: 100%

   * - 参数
     - 说明

   * - :mc-cmd:`ALIAS <mc ilm rule add ALIAS>`
     - 指定要为其创建生命周期管理规则的 MinIO 部署
       :mc:`alias <mc alias>`。

   * - :mc-cmd:`BUCKET <mc ilm rule add ALIAS>`
     - 指定要为其创建生命周期管理规则的存储桶完整路径。

   * - :mc-cmd:`TIERNAME <mc ilm rule add --transition-tier>`
     - MinIO 将对象转移到的远程存储层。
       指定在上一步中创建的远程存储层名称。

       如果要将非当前对象版本转移到不同的远程层，
       请为 :mc-cmd:`~mc ilm rule add --noncurrent-transition-tier`
       指定另一个层名称。

   * - :mc-cmd:`DAYS <mc ilm rule add --transition-days>`
     - MinIO 在经过多少个日历天后将对象标记为可转移。
       该值必须为整数，例如 ``30`` 表示 30 天。

   * - :mc-cmd:`NONCURRENT_DAYS <mc ilm rule add --noncurrent-transition-days>`
     - MinIO 在经过多少个日历天后将非当前对象版本标记为可转移。
       MinIO 具体计算的是对象变为非当前版本后的时间，
       而不是对象创建时间。该值必须为整数，
       例如 ``90`` 表示 90 天。

       省略此值可忽略非当前对象版本。

       此选项对未启用版本控制的存储桶无效。

.. end-create-transition-rule-desc

.. start-create-transition-user-desc

此步骤会在 MinIO 部署上创建用于支持生命周期管理操作的用户和策略。
如果该部署已经存在具备所需 |permissions| 的用户，则可以跳过此步骤。

以下示例使用 ``Alpha`` 作为 MinIO 部署 :mc:`alias <mc alias>` 的占位符。
请将其替换为配置生命周期管理规则时所使用的 MinIO 部署别名。
同时，请按照所在组织的密码生成最佳实践，
将密码 ``LongRandomSecretKey`` 替换为足够长、随机且安全的密钥。

.. code-block:: shell
   :class: copyable

   wget -O - https://minio.pigsty.io/examples/LifecycleManagementAdmin.json | \
   mc admin policy create Alpha LifecycleAdminPolicy /dev/stdin
   mc admin user add Alpha alphaLifecycleAdmin LongRandomSecretKey
   mc admin policy attach Alpha LifecycleAdminPolicy --user=alphaLifecycleAdmin

此示例假定所指定的 alias 具有在该部署上创建策略和用户所需的权限。
有关 MinIO 用户和策略的更完整文档，
请分别参见 :ref:`minio-users`
和 :ref:`MinIO Policy Based Access Control <minio-policy>`。

.. end-create-transition-user-desc

.. start-transition-bucket-access-desc

MinIO 要求对远程存储层上的已转移数据拥有独占访问权限。
"hot" MinIO 源端上的对象元数据与远程 "warm/cold" 层上的对象数据紧密关联。
如果无法访问远程层，MinIO 就无法检索对象数据；
同样，远程层也不能用于恢复源端丢失的元数据。

对已转移对象的所有访问都必须仅通过 MinIO 发起的 S3 API 操作完成。
手动修改已转移对象时，无论修改的是 "hot" MinIO 层上的元数据，
还是远程 "warm/cold" 层上的对象数据，都可能导致该对象的数据丢失。

对于远程存储桶或存储桶前缀中不受该 MinIO 部署明确管理的任何对象，
MinIO 都会将其忽略。
自动转移与透明对象检索依赖以下前提：

- 不会在远程存储上由外部修改、迁移或删除对象。
- 远程存储桶上不存在生命周期管理规则
  （例如转移或过期）。

MinIO 会将所有已转移对象存储在远程存储桶或资源下、
每个部署唯一的前缀值之中。
该值并非用于在后端识别源部署。
在配置远程目标时，MinIO 还支持附加一个可选的人类可读前缀，
这可能有助于诊断、维护或灾难恢复相关操作。

对于包含其他数据的远程存储层，
包括来自其他 MinIO 部署的已转移对象，
MinIO 建议指定此可选前缀。
本教程包含设置此前缀所需的语法。

.. end-transition-bucket-access-desc

.. start-transition-data-loss-desc

MinIO 分层行为依赖远程存储在收到请求后立即返回对象
（毫秒到秒级）。
因此，MinIO 不支持需要 rehydration、等待窗口
或人工干预的远程存储。

MinIO 会为每个已转移对象创建元数据，用于标识其在远程存储中的位置。
应用程序无法脱离 MinIO 直接识别和访问已转移对象。
因此，已转移数据的可用性仍依赖于
:ref:`纠删码 <minio-erasure-coding>` 和分布式部署拓扑
为 MinIO 部署中所有对象提供的核心保护能力。
使用对象转移不会带来任何额外的业务连续性或灾难恢复收益。

需要 :abbr:`BC/DR (Business Continuity/Disaster Recovery)`
保护的工作负载应实现 MinIO :ref:`Server-Side replication
<minio-bucket-replication-serverside>`。
复制可确保对象保存在远程复制站点上，
从而使用户能够在发生部分或全部数据丢失时从远端重新同步。
有关如何使用复制在部分或全部数据丢失后恢复的更完整文档，
请参见 :ref:`minio-replication-behavior-resync`。

.. end-transition-data-loss-desc
