============
核心管理概念
============

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

以下核心概念是管理 MinIO 部署的基础，包括但不限于对象保留、加密和访问管理。

什么是对象存储？
----------------

.. _objects:

:ref:`对象 <objects>` 是二进制数据，有时也称为 Binary Large OBject (BLOB)。
Blob 可以是图像、音频文件、电子表格，甚至是二进制可执行代码。
像 MinIO 这样的对象存储平台提供专门的工具和能力，用于存储、检索和搜索 Blob。

.. _buckets:

MinIO 对象存储使用 :ref:`存储桶 <buckets>` 来组织对象。
存储桶类似于文件系统中的文件夹或目录，每个存储桶都可以容纳任意数量的对象。
MinIO 存储桶提供与 AWS S3 存储桶相同的功能。

例如，考虑一个承载 Web 博客的应用程序。
该应用程序需要存储多种 Blob，包括视频和图像等富媒体内容。

MinIO 通过 ``prefixing`` 功能支持多级嵌套目录，从而支持变化最频繁的对象存储工作负载。


MinIO 如何确定对对象的访问权限？
--------------------------------

MinIO 要求客户端在每次新操作时同时完成身份验证和授权。
因此，:ref:`身份与访问管理（IAM） <minio-authentication-and-identity-management>` 是 MinIO 配置中的关键组成部分。

*身份验证* 用于验证连接客户端的身份。
MinIO 要求客户端使用 :s3-api:`AWS Signature Version 4 协议 <sig-v4-authenticating-requests.html>` 进行身份验证，并支持已弃用的 Signature Version 2 协议。
具体而言，客户端必须提供有效的 access key 和 secret key，才能访问任何 S3 或 MinIO 管理 API，例如 ``PUT``、``GET`` 和 ``DELETE`` 操作。

然后，MinIO 会检查已通过身份验证的用户或客户端是否具有在部署上执行操作或使用资源的 *授权*。
MinIO 使用 :ref:`基于策略的访问控制（PBAC） <minio-policy>`，其中每项策略描述一条或多条规则，用于定义用户或用户组的权限。
创建策略时，MinIO 支持 S3 特定的 :ref:`操作 <minio-policy-actions>` 和 :ref:`条件 <minio-policy-conditions>`。

默认情况下，MinIO 会 *拒绝* 访问用户已分配或继承策略中未明确引用的操作或资源。

MinIO 将访问管理功能作为软件的一部分提供。
或者，你可以将 MinIO 配置为通过 :ref:`Active Directory/LDAP <minio-external-identity-management-ad-ldap>` 或 :ref:`OpenID/OIDC <minio-external-identity-management-openid>` 之一与多个外部 IAM 提供方进行身份验证。


MinIO 如何保护数据？
--------------------

MinIO 支持在对象位于磁盘上时（encryption-at-rest）以及从一个位置传输到另一个位置期间（encryption-in-transit，或 "in flight"）对对象进行加密的方法。
启用后，MinIO 使用 :ref:`服务器端加密 <minio-encryption-overview>` 以加密状态写入对象。
要检索并读取加密对象，用户必须具备适当的访问权限，并提供对象的解密密钥。

MinIO 支持 **Transport Layer Security** (TLS) 1.2 和 1.3，可对对象传输进行加密。
TLS 取代了此前使用且现已弃用的 Secure Socket Layer (SSL) 方法。
TLS 标准由 Internet Engineering Task Force (IETF) 维护，定义了互联网通信所使用的标准，以支持加密、身份验证和数据完整性。

对用户进行身份验证并验证其对象访问权限的过程称为 `TLS Handshake`。
身份验证完成后，TLS 提供用于加密和解密服务器与请求客户端之间信息传输的密码机制。

MinIO 支持多种 :ref:`服务器端加密 <minio-encryption-overview>` 方法。

.. _minio-admin-concepts-organize-objects:

我可以在存储桶内按文件夹结构组织对象吗？
----------------------------------------

MinIO 为每个对象使用 :term:`prefix` 方法，以模拟传统文件系统中的文件夹结构。
前缀化是指在对象名称前附加一个固定字符串。

使用前缀时，无需手动创建文件夹和子文件夹。
相反，MinIO 会在对象名称的前缀中查找 ``/`` 字符。
每个 ``/`` 都表示一个新的文件夹或子文件夹。

MinIO 使用对象名称及其前缀，自动为已存储对象生成一系列文件夹和子文件夹。
当你在多个对象上使用相同的前缀字符串时，MinIO 会将其识别为相似对象或分组对象。

例如，名为 ``/articles/john.doe/2022-01-02-MinIO-Object-Storage.md`` 的对象最终会位于 ``articles`` 存储桶中名为 ``john.doe`` 的文件夹下。

一个 MinIO 对象存储可能类似于以下结构，其中包含三个存储桶。
MinIO 会根据这些对象的前缀在 ``articles`` 存储桶中自动生成两个文件夹。

.. code-block:: text

   / #root
   /images/
      2022-01-02-MinIO-Diagram.png
      2022-01-03-MinIO-Advanced-Deployment.png
      MinIO-Logo.png
   /videos/
      2022-01-04-MinIO-Interview.mp4
   /articles/
      /john.doe/
         2022-01-02-MinIO-Object-Storage.md
         2022-01-02-MinIO-Object-Storage-comments.json
      /jane.doe/
         2022-01-03-MinIO-Advanced-Deployment.png
         2022-01-02-MinIO-Advanced-Deployment-comments.json
         2022-01-04-MinIO-Interview.md

MinIO 本身不限制任何特定前缀可包含的对象数量。
但在前缀规模较大时，硬件和网络条件可能会对性能产生影响。

- 对于采用普通或预算导向型硬件的部署，工作负载架构应以每个前缀 10,000 个对象作为基线目标。
  再根据真实工作负载的基准测试和监控结果，将该目标提高到硬件能够有效承载的上限。
- 使用高性能或企业级 :ref:`硬件 <deploy-minio-distributed-recommendations>` 的部署通常可以处理包含数百万个甚至更多对象的前缀。

|SUBNET| 企业账户可以将年度架构审查纳入部署和维护策略，以确保依赖 MinIO 的项目在长期内保持性能并获得成功。

有关限制前缀内容所带来收益的更深入讨论，请参阅 :s3-docs:`优化 S3 性能 <optimizing-performance.html>` 一文。

如何在 MinIO 上备份和恢复对象？
--------------------------------

MinIO 提供两种复制类型，用于将对象、其版本及其元数据从一个位置复制到另一个位置。
你可以在 :ref:`存储桶级别 <minio-bucket-replication>` 或 :ref:`站点级别 <minio-site-replication-overview>` 配置复制。

- 存储桶级别复制既可以作为单向、主动-被动复制运行（例如用于归档），也可以作为双向、主动-主动复制运行，以保持两个存储桶彼此同步。
- 站点级别复制以双向、主动-主动复制方式运行，以保持多个数据位置（例如不同地理位置的数据中心）彼此同步。

除复制外，MinIO 还提供镜像服务。
:mc:`mc mirror` 仅将实际对象复制到任何其他兼容 S3 的数据存储中，包括其他 MinIO 存储。
但是，版本和元数据不会随 :mc:`mc mirror` 命令一起备份。

.. include:: /includes/common-admonitions.rst
   :start-after: start-exclusive-drive-access
   :end-before: end-exclusive-drive-access

MinIO 提供哪些工具可根据访问速度和频率管理对象？
------------------------------------------------

:ref:`分层规则 <minio-lifecycle-management-tiering>` 允许将频繁访问的对象存储在热存储或温存储上，这类存储通常成本更高，但可提供更好的性能。

较少访问的对象可以迁移到冷存储。
冷存储通常以较低成本换取较慢性能。


MinIO 如何保护对象免受意外覆盖或删除？
--------------------------------------

锁定
~~~~

锁是一种 Write Once Read Many (WORM) 机制，可防止对象被删除或修改。
对象被锁定后，MinIO 会无限期保留这些对象，直到有人移除锁或锁到期。

MinIO 提供：

- :ref:`法律保留 <minio-object-locking-legalhold>`：面向所有用户的无限期保留锁
- :ref:`合规保留 <minio-object-locking-compliance>`：面向所有用户的基于时间的限制
- :ref:`治理锁 <minio-object-locking-governance>`：面向非特权用户的基于时间的规则

版本控制
~~~~~~~~

默认情况下，以相同名称（包括前缀）写入的对象会覆盖同名的现有对象。
MinIO 提供可创建启用版本控制的存储桶的配置选项。
:ref:`版本控制 <minio-bucket-versioning>` 使你可以访问某个唯一命名对象随时间变化产生的不同版本。
启用后，MinIO 会将发生变更的对象写入与原始对象不同的版本，从而允许同时访问原始对象和更新后的对象。

MinIO 存储桶上的其他配置决定了存储桶中每个对象的旧版本保留时长。
