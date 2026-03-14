.. start-replication-encrypted-objects

MinIO 支持复制使用 :ref:`SSE-KMS <minio-encryption-sse-kms>` 和
:ref:`SSE-S3 <minio-encryption-sse-s3>` 加密的对象：

- 对于使用 SSE-KMS 加密的对象，MinIO *要求* 目标存储桶支持使用与源存储桶对象
  加密时 *相同的密钥名称* 对对象执行 SSE-KMS 加密。

- 对于使用 :ref:`SSE-S3 <minio-encryption-sse-s3>` 加密的对象，MinIO *要求*
  目标存储桶同样支持 SSE-S3 对象加密，而不考虑密钥名称。

在复制过程中，MinIO 会先在源存储桶上对对象进行 *解密*，再通过网络传输未加密的
对象。目标 MinIO 部署随后会使用目标端的加密设置重新对该对象进行加密。
因此，MinIO *强烈建议* 在源端和目标端部署上都
:ref:`启用 TLS <minio-TLS>`，以确保对象在传输过程中的安全。

MinIO *不* 支持复制客户端加密对象（SSE-C）。

.. end-replication-encrypted-objects

.. start-replication-minio-only

MinIO 服务端复制仅适用于 MinIO 部署之间。
源端和目标端部署 *都必须* 运行版本匹配的 MinIO Server。

如需在任意 S3 兼容服务之间配置复制，请使用 :mc:`mc mirror`。

.. end-replication-minio-only

.. start-replication-requires-versioning

MinIO 依赖 :ref:`版本控制 <minio-bucket-versioning>` 提供的不可变性保护来支持
复制和重新同步。

使用 :mc:`mc version info` 验证源存储桶和远端存储桶的版本控制状态。
必要时使用 :mc:`mc version enable` 命令启用版本控制。

如果你在源存储桶中将某个前缀或文件夹排除在版本控制之外，MinIO 就无法复制该文件夹
或前缀中的对象。

.. end-replication-requires-versioning

.. start-replication-requires-object-locking

MinIO 支持复制受 :ref:`WORM 锁定 <minio-object-locking>` 保护的对象。
两个复制存储桶 *都必须* 启用对象锁定，MinIO 才能复制被锁定的对象。
对于主动-主动配置，MinIO 建议在两个存储桶上使用 *相同的* 保留规则，以确保跨站点
行为一致。

根据 S3 的行为要求，你必须在创建存储桶时启用对象锁定。
随后，你可以在任何时候配置对象保留规则。
在开始此过程 *之前*，请先在状态异常的目标存储桶上配置所需规则。

.. end-replication-requires-object-locking

.. start-replication-required-permissions

要配置和启用复制规则，存储桶复制要求源端和目标端部署具备特定权限。

.. tab-set::

   .. tab-item:: 复制管理员

      下列策略提供了在部署上配置和启用复制所需的权限。

      .. literalinclude:: /extra/examples/ReplicationAdminPolicy.json
         :class: copyable
         :language: json

      - ``"EnableRemoteBucketConfiguration"`` 语句授予创建远端目标以支持复制的权限。

      - ``"EnableReplicationRuleConfiguration"`` 语句授予在存储桶上创建复制规则的权限。
        ``"arn:aws:s3:::*`` 资源会将复制权限应用到源部署上的 *任意* 存储桶。
        你可以按需将该用户策略限制到特定存储桶。

      下列代码使用所需策略创建一个 :ref:`MinIO 管理用户 <minio-users>`。
      将 ``TARGET`` 替换为你要配置复制的 MinIO 部署的 :ref:`别名 <alias>`：

      .. code-block:: shell
         :class: copyable

         wget -O - https://silo.pigsty.cc/examples/ReplicationAdminPolicy.json | \
         mc admin policy create TARGET ReplicationAdminPolicy /dev/stdin
         mc admin user add TARGET ReplicationAdmin LongRandomSecretKey
         mc admin policy attach TARGET ReplicationAdminPolicy --user=ReplicationAdmin

      对于配置了
      :ref:`Active Directory/LDAP <minio-external-identity-management-ad-ldap>`
      或 :ref:`OpenID Connect <minio-external-identity-management-openid>`
      用户管理的 MinIO 部署，则应改为为存储桶复制创建专用
      :ref:`访问密钥 <minio-idp-service-account>`。

   .. tab-item:: 复制远端用户

      下列策略提供了将复制数据同步 *到* 该部署所需的权限。

      .. literalinclude:: /extra/examples/ReplicationRemoteUserPolicy.json
         :class: copyable
         :language: json

      - ``"EnableReplicationOnBucket"`` 语句授予远端目标获取存储桶级配置的权限，
        从而支持在 MinIO 部署中 *所有* 存储桶上执行复制操作。
        如果要将策略限制到特定存储桶，请像 ``"arn:aws:s3:::bucketName"`` 一样，
        在 ``Resource`` 数组中指定这些存储桶。

      - ``"EnableReplicatingDataIntoBucket"`` 语句授予远端目标将数据同步到 MinIO
        部署中 *任意* 存储桶的权限。
        如果要将策略限制到特定存储桶，请像 ``"arn:aws:s3:::bucketName/*"`` 一样，
        在 ``Resource`` 数组中指定这些存储桶。

      下列代码使用所需策略创建一个 :ref:`MinIO 管理用户 <minio-users>`。
      将 ``TARGET`` 替换为你要配置复制的 MinIO 部署的 :ref:`别名 <alias>`：

      .. code-block:: shell
         :class: copyable

         wget -O - https://silo.pigsty.cc/examples/ReplicationRemoteUserPolicy.json | \
         mc admin policy create TARGET ReplicationRemoteUserPolicy /dev/stdin
         mc admin user add TARGET ReplicationRemoteUser LongRandomSecretKey
         mc admin policy attach TARGET ReplicationRemoteUserPolicy --user=ReplicationRemoteUser

      对于配置了
      :ref:`Active Directory/LDAP <minio-external-identity-management-ad-ldap>`
      或 :ref:`OpenID Connect <minio-external-identity-management-openid>`
      用户管理的 MinIO 部署，则应改为为存储桶复制创建专用
      :ref:`访问密钥 <minio-idp-service-account>`。

关于在 MinIO 部署中添加用户、访问密钥和策略的更完整文档，请参见
:mc:`mc admin user`、:mc:`mc admin user svcacct` 和 :mc:`mc admin policy`。

.. end-replication-required-permissions

.. start-replication-sync-vs-async

对于给定的远端目标，MinIO 支持指定异步复制（默认）或同步复制。

在异步复制模式下，MinIO 会在将对象放入
:ref:`复制队列 <minio-replication-process>` *之前* 完成发起的 ``PUT`` 操作。
因此，发起请求的客户端可能会在对象完成复制 *之前* 就看到 ``PUT`` 操作成功。
虽然这可能导致远端对象陈旧或缺失，但它降低了因复制负载而导致写入变慢的风险。

在同步复制模式下，MinIO 会在完成发起的 ``PUT`` 操作 *之前* 尝试复制对象。
无论复制尝试是否成功，MinIO 都会返回一个成功的 ``PUT`` 操作结果。
这降低了写入变慢的风险，但代价是远端位置可能出现陈旧或缺失对象。

.. end-replication-sync-vs-async

.. start-mc-admin-replicate-what-replicates

每个 MinIO 部署（“对等站点”）都会将以下变更同步到其他对等站点：

- 存储桶和对象的创建、修改与删除，包括

  - 存储桶与对象配置
  - :ref:`策略 <minio-policy>`
  - :mc:`mc tag set`
  - :ref:`锁定 <minio-object-locking>`，包括保留和 legal hold 配置
  - :ref:`加密设置 <minio-encryption-overview>`

- IAM 用户、组、策略以及策略到用户或组的映射（针对 LDAP 用户或组）的创建与删除
- 为可由本地 ``root`` 凭证验证的会话令牌创建 Security Token Service (STS) 凭证
- :ref:`访问密钥 <minio-mc-admin-user-svcacct>` 的创建与删除
  （不包括 ``root`` 用户拥有的访问密钥）

站点复制会在所有复制站点上，为所有新建和现有存储桶启用
:ref:`存储桶版本控制 <minio-bucket-versioning>`。

.. versionadded:: mc RELEASE.2023-12-02T02-03-28Z

你可以选择在对等站点之间复制 ILM 过期规则。
对于新的站点复制配置，可使用带有
:mc-cmd:`~mc admin replicate add --replicate-ilm-expiry` 标志的
:mc-cmd:`mc admin replicate add`。
对于现有站点复制配置，则可根据需要使用 :mc-cmd:`mc admin replicate update`
配合 :mc-cmd:`~mc admin replicate update --enable-ilm-expiry-replication`
或 :mc-cmd:`~mc admin replicate update --disable-ilm-expiry-replication`
标志启用或禁用该行为。

.. end-mc-admin-replicate-what-replicates

.. start-mc-admin-replicate-what-does-not-replicate

处于站点复制配置中的 MinIO 部署 *不会* 复制以下项目的创建或修改：

- :ref:`存储桶通知 <minio-bucket-notifications>`
- :ref:`生命周期管理（ILM）配置 <minio-lifecycle-management>`
- :ref:`站点配置设置 <minio-mc-admin-config>`

.. end-mc-admin-replicate-what-does-not-replicate

.. start-mc-admin-replicate-load-balancing

指定该站点的负载均衡器、反向代理或类似网络控制平面组件的 URL 或 IP 地址。
请求会自动路由到部署中的各个节点。

MinIO 不建议为对等站点使用单个节点主机名。
这会形成单点故障：如果该节点离线，复制就会失败。

.. end-mc-admin-replicate-load-balancing
