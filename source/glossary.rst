========
术语表
========

.. default-domain:: minio

.. glossary::
   :sorted:

   access keys
   访问密钥
     MinIO 部署或租户中的一种受限用户账户，通常用于 API 调用。
     Access Keys 过去称为 "Service Accounts"。

   active-active
   双活
     一种 :term:`replication` 方式，提供数据的双向镜像。
     在 active-active 配置中，修改任一存储位置中的数据，也会同步修改其他存储位置中的数据。
     
     另请参见：:term:`active-passive`。

   active-passive
   主备
     一种 :term:`replication` 方式，提供数据的单向镜像。
     在 active-passive 配置中，修改源位置的数据也会同步修改目标位置的数据。
     但对目标位置数据的更改不会影响源位置的数据。
     
     另请参见：:term:`active-active`。

   alias
   别名
     本地定义的 MinIO 部署引用，用于大多数命令行接口操作。
     参见 :mc:`mc alias set`。

   audit logs
   审计日志
     对 MinIO 部署中每项操作的细粒度记录。
     :ref:`Audit logs <minio-logging>` 有助于满足要求对操作进行详细跟踪的安全标准和法规。
     
     另请参见：:term:`server logs`。

   bit rot 
   位腐坏
     在用户不知情的情况下发生的数据损坏。
     
     MinIO 通过 :term:`hashing` 和 :term:`erasure coding` 来应对 bit rot。

   bit rot healing
   位腐坏自愈
     因 bit rot 而损坏的对象会在任何 ``GET`` 或 ``HEAD`` 操作期间自动 :term:`healed <healing>`。
     MinIO 借助其 :term:`hashing` 实现，在访问过程中实时检测并修复损坏对象。

   bucket
   buckets
   存储桶
     :term:`objects` 及相关配置的逻辑分组。

   cluster
   集群
     由一组磁盘以及一个或多个 MinIO server 进程组成，并汇聚为单一存储资源的集合。
     
     另请参见：:term:`tenant`。

   cluster registration
   集群注册
     Cluster registration 会将 MinIO 部署关联到 :term:`SUBNET` `subscription <https://min.io/pricing?jmp=docs>`__。
     一个组织可以将多个 MinIO 集群注册到同一个 SUBNET 订阅。

   Console
   MinIO Console
   MinIO 控制台
     用于与 MinIO 部署或 :term:`tenant` 交互的图形用户界面 (GUI)。

   data
   数据块
     MinIO 在执行 :term:`erasure coding` 时写入的两类块之一。
     data 块包含文件内容。

     当 data 块损坏或丢失时，:term:`parity` 块可用于重建数据。

   decommission
   下线
     从 :term:`distributed` 部署中移除一个磁盘池的过程。
     启动后，待下线池中的对象会迁移到该部署中的其他池，从而完成排空。
     
     该过程不可逆。
   
   deployment
   部署
     一个具体的 MinIO 实例，包含一组 :term:`buckets` 和 :term:`objects`。

   disk encryption
   磁盘加密
     将写入磁盘的全部内容转换为未经授权实体难以解读的值。
     磁盘加密可与其他加密技术结合使用，以构建稳健的数据安全体系。

   enclave
   隔离域
     对有状态 Key Encryption Service (KES) 服务器中隔离区域的称谓。
     一个 KES 服务器可以有一个或多个 enclave。
     KES 服务器中的每个 enclave 都持有独立的密钥、策略和管理身份。
     一个 enclave 无法查看或使用服务器上的其他 enclave。

     例如，你可以使用多个 enclave，在单个有状态 KES 服务器中为多个 MinIO 租户保存彼此完全隔离的密钥库。

   encryption at rest
   静态加密
     一种以加密状态存储对象的加密方式。
     对象在未从一个位置移动到另一个位置时始终保持加密状态。

     服务器可使用以下密钥管理方式之一对对象进行加密：
     :term:`SSE-KMS`、:term:`SSE-S3` 或 :term:`SSE-C`。

   encryption in transit
   传输加密
     一种在对象从一个位置传输到另一个位置时保护对象的加密方式，例如在 GET 请求期间。
     对象在源端或目标存储设备上可以是加密状态，也可以不是。
   
   erasure coding
   纠删码
     一种将 :term:`objects` 拆分为多个分片，并将这些分片写入多个独立磁盘的技术。
     
     根据所使用的 :term:`topology`，纠删码允许 MinIO 部署在丢失部分磁盘或节点的情况下仍保留读写能力。

   erasure set
   纠删码集合
     MinIO 中支持 :term:`erasure coding` 的一组磁盘。
     MinIO 会将部署中 server pool 的磁盘划分为若干组，每组包含 4 到 16 个磁盘，每组构成一个 *erasure set*。
     写入对象时，:term:`data` 和 :term:`parity` 块会随机写入该 erasure set 中的各个磁盘。

   hashing
   哈希
     使用算法创建唯一且定长的字符串（即一个 `value`）来标识一段数据。
   
   healing
   自愈
     从 bit rot、磁盘故障或站点故障导致的部分数据丢失中恢复数据的过程。

   health diagnostics
   健康诊断
     一组 MinIO :ref:`API endpoints <minio-healthcheck-api>`，用于检查服务器是否：
     
     - 在线
     - 可用于写入数据
     - 可用于读取数据
     - 可在不影响集群读写操作的情况下进入维护状态

   host bus adapter
   HBA 
   主机总线适配器
     连接主机系统与存储设备的电路板或集成电路适配器。
     :abbr:`HBA (host bus adapter)` 负责执行相关处理，以减轻主机系统处理器的负载。

   IAM integration
   IAM 集成
     MinIO 仅允许经过身份验证的用户访问数据。
     MinIO 提供内置的身份管理方案来创建授权凭据。
     此外，MinIO 用户还可以选择使用来自第三方身份提供者 (IDP) 的凭据进行身份验证，包括 OpenID 或 LDAP 提供者。

   JBOD 
   直通磁盘组
     “Just A Bunch of Drives”的首字母缩写。
     JBOD 是一种可容纳多个硬盘的存储设备机箱。
     这些磁盘可以组合成一个逻辑存储单元。
     
     另请参见：:term:`RAID`

   lifecycle management
   ILM
   生命周期管理
     用于确定 :term:`objects` 何时迁移或过期的一组规则。

   locking
   锁定
     一种规则，用于防止对象在授权代理移除该规则或规则到期之前被移除或删除。

   monitoring
   监控
     对 MinIO 集群、部署、租户或服务器的状态、活动和可用性进行审查的行为。
     MinIO 提供以下工具：

     - 与 `Prometheus <https://prometheus.io/>`__ 兼容的指标与告警
     - :term:`Audit logs`
     - :term:`server logs`
     - :ref:`Healthcheck API endpoints <minio-healthcheck-api>`
     - :ref:`Bucket notifications <minio-bucket-notifications>`

   multi-node multi-drive
   MNMD
   多机多盘
   distributed
   分布式
     一种系统 :term:`topology`，使用多台服务器且每台服务器配备多个磁盘来承载 MinIO 实例。
     对于分布式部署，MinIO 建议使用 Kubernetes。

   multipart upload
   分段上传
     Multipart upload 是一种由客户端发起的 :s3-docs:`S3 function <mpuoverview.html>`，它将单个对象拆分为多个 part，以便从一个位置传输到另一个位置。
     客户端将每个 part 独立上传到 MinIO，而 MinIO 负责将这些已接收的 part 重建为原始对象。

     Multipart upload 的优势包括更高的吞吐量以及更强的网络错误恢复能力。
     对于实际或预估大小超过 100MB 的对象，使用 multipart upload 通常能获得最佳效果。
     
     更多细节参见 :s3-docs:`Amazon AWS documentation <mpuoverview.html>`。

   network encryption
   网络加密
     一种在数据从一个位置传输到另一个位置时保护数据的方式，例如 server-server 或 client-server 之间的传输。
     MinIO 对入站和出站流量均支持 :ref:`Transport Layer Security (TLS) <minio-tls>` 1.2 及更高版本。

   object
   objects
   对象
     MinIO 通过兼容 S3 的 API 交互的数据项。
     对象可以分组到 :term:`buckets` 中。

   Operator
   Operator Console
   Operator 控制台
     用于在分布式部署环境中部署和管理 MinIO :term:`tenants` 的图形用户界面 (GUI)。

   parity
   校验块
     MinIO 为对象写入的块中，用于在数据块缺失或损坏时支持数据重建的部分。
     parity 块的数量表示部署在仍保持读写能力的前提下，可丢失的 :term:`erasure set` 磁盘数量。

   prefix
   前缀
     Prefix 通过为应共享相似层级或结构的对象分配相同字符串，来组织 :term:`bucket` 中的 :term:`objects`。
     使用定界字符（通常为 `/`）可以增加层级。
     虽然带有 prefix 的对象在某些文件系统中可能看起来像目录结构，但 prefix 并不是目录。

     MinIO 本身不会限制某个特定 prefix 可包含的对象数量。
     但对于较大的 prefix，硬件和网络条件可能会带来性能影响。

     - 对于硬件配置一般或预算导向的部署，应将每个 prefix 约 10,000 个对象作为工作负载设计的基线。
       然后根据真实工作负载的基准测试和监控结果，在硬件可有效承载的范围内逐步提高这一目标。
     - 对于高性能或企业级 :ref:`hardware <deploy-minio-distributed-recommendations>`，通常可以处理包含数百万个甚至更多对象的 prefix。

     |SUBNET| 企业版账户可将年度架构评审纳入部署和维护策略，以确保依赖 MinIO 的项目获得长期性能与成功。
    
   RAID
   磁盘阵列
     “Redundant Array of Independent Disks”的首字母缩写。
     该技术将多个独立物理磁盘合并为一个存储单元或阵列。
     某些 RAID 级别通过在物理磁盘之间复制、条带化或镜像数据来提供数据冗余或容错能力。
     
     另请参见：:term:`JBOD`。

   read quorum
   读仲裁
     执行读操作时，重建完整对象所需的最少对象分片数量。
     更多信息参见 :ref:`minio-ec-basics`。

   replication
   mirror
   复制
   镜像复制
     将 :ref:`bucket <minio-bucket-replication>` 或整个 :ref:`site <minio-site-replication-overview>` 复制到另一位置的行为。

   scanner 
   MinIO Scanner
   扫描器
     MinIO 运行的若干低优先级进程之一，用于检查：
     
     - 需要执行对象迁移的生命周期管理规则
     - 存储桶或站点复制状态
     - 对象 :term:`bit rot` 和 :term:`healing`
     - 使用量数据

     更多信息参见 :ref:`minio-concepts-scanner`。

   self signed certificates
   自签名证书
     自签名证书是由负责证书所保护内容的公司或开发者自行创建、签发并签名的证书。
     自签名证书并非由公开受信任的第三方证书颁发机构 (CA) 签发或签名。
     这类证书不会过期，无需定期审查，也无法被吊销。

   server logs
   服务器日志
     记录到系统控制台的 ``minio server`` 操作日志。
     :ref:`Server logs <minio-logging>` 支持常规监控和故障排查。

     更详细的日志信息参见 :term:`audit logs`。

   server pool
   pool
   服务器池
     一组 ``minio server`` 节点，它们组合各自的磁盘和资源，以支持对象存储与检索请求。
    
     更多信息参见 :ref:`minio-intro-server-pool`。

   service account
   服务账户
     已更名为 :term:`access keys`。
     MinIO 部署或租户中的一种受限用户账户，通常用于 API 调用。

   shard
   shards
   分片
     对象经过 MinIO :term:`erasure coded <erasure coding>` 后形成的一部分。
     每个 "shard" 都表示 data 或 parity，供 MinIO 在读请求时用于重建对象。

     .. include:: /includes/common-admonitions.rst
        :start-after: start-exclusive-drive-access
        :end-before: end-exclusive-drive-access
     
     更详细的信息参见 :ref:`minio-erasure-coding`。

   single-node multi-drive
   SNMD
   单机多盘
     一种系统 :term:`topology`，在单个计算资源上使用多个挂载磁盘部署 MinIO。

   single-node single-drive
   SNSD
   单机单盘
   filesystem
   文件系统模式
     一种系统 :term:`topology`，在单个计算资源上使用单个磁盘部署 MinIO。
     这为原本标准的文件系统增加了 S3 类型的功能。

   SSE-C
     一种 :term:`encryption at rest` 方式，在写入对象时使用随写请求提供的加密密钥对对象进行加密。
     读取对象时，必须提供与最初写入该对象时相同的加密密钥。
     此外，还必须自行管理所使用的加密密钥。

     另请参见：:term:`SSE-KMS`、:term:`SSE-S3`、:term:`encryption at rest`、:term:`network encryption`。

   SSE-KMS
     一种 :term:`encryption at rest` 方式，在写入时使用由服务提供方管理的独立密钥对每个对象进行加密。
     可以使用存储桶级（默认）或对象级密钥。
     对于加密密钥管理，MinIO 推荐使用 SSE-KMS。

     另请参见：:term:`SSE-S3`、:term:`SSE-C`、:term:`encryption at rest`、:term:`network encryption`。

   SSE-S3
     一种 :term:`encryption at rest` 方式，在写入时使用一个单一密钥为部署中的所有对象进行加密。
     一个部署使用同一个外部密钥来解密该部署中的任意对象。

     另请参见：:term:`SSE-KMS`、:term:`SSE-C`、:term:`encryption at rest`、:term:`network encryption`。

   standalone deployment
   独立部署
     一个 :term:`single-node single-drive` (SNSD) MinIO 部署。
     该术语此前用于指代已弃用的 :ref:`Gateway or Filesystem Mode <minio-gateway-migration>` 部署类型。

   SUBNET
     `MinIO's Subscription Network <https://min.io/pricing?jmp=docs>`__ 用于跟踪支持工单，并为订阅账户提供 24 小时直连工程师支持。

   tenant
   tenants
   租户
     在 :term:`distributed` 模式下，指某个具体的 MinIO 部署。
     一个 MinIO Operator 实例可以拥有多个 tenant。

   topology
   拓扑
     部署使用的硬件配置。
     MinIO 支持三种 topology：
     
     - :term:`multi-node multi-drive`
     - :term:`single-node multi-drive`
     - :term:`single-node single-drive`

   versioning
   版本化
     在 :term:`object` 随时间变化的过程中保留其多个迭代版本。
  
   webhook
   Webhook 回调
     :ref:`webhook <minio-bucket-notifications-publish-webhook>` 是一种通过自定义回调改变网页或 Web 应用行为的方法。
     其格式通常为通过 HTTP POST 请求发送的 :abbr:`JSON (JavaScript Object Notation)`。

   write quorum
   写仲裁
     执行写操作时，MinIO 必须成功写入到 :ref:`erasure set <minio-ec-erasure-set>` 的最少对象分片数量。
     更多信息参见 :ref:`minio-ec-basics`。

   WORM
   一次写入多次读取
     Write Once Read Many (WORM) 是一种数据保留方法，作为对象锁定的一部分发挥作用。
     许多请求都可以检索并查看启用 WORM 锁定的对象（``read many``），但任何写请求都不能更改该对象（``write once``）。
