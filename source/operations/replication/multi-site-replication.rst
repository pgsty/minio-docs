.. _minio-site-replication-overview:

====================
站点复制概览
====================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 1

站点复制将多个相互独立的 MinIO 部署配置为一个由副本组成的集群，这些副本称为对等站点。

   .. figure:: /images/architecture/architecture-load-balancer-multi-site.svg
      :figwidth: 100%
      :alt: Diagram of a site replication deployment with two sites

      一个包含两个对等站点的站点复制部署。
      负载均衡器负责将操作路由到两个站点中的任意一个。
      写入一个站点的数据会自动复制到另一个对等站点。

站点复制要求使用内置的 MinIO Identity Provider (IDP) *或* 外部 IDP。
所有已配置的部署都必须使用同一个 IDP。
使用外部 IDP 的部署必须在各站点之间使用相同的配置。

有关站点复制架构和部署概念的更多信息，请参阅 :ref:`Deployment Architecture: Replicated MinIO Deployments <minio-deployment-architecture-replicated>`。

除早期开发、评估或一般性实验外，MinIO 不建议在站点复制中使用 MacOS、Windows 或未编排的容器化部署。
生产环境请使用 :minio-docs:`Linux <minio/linux/operations/install-deploy-manage/multi-site-replication.html>` 或 :minio-docs:`Kubernetes <minio/kubernetes/upstream/operations/install-deploy-manage/multi-site-replication.html>`

概览
----

.. _minio-site-replication-what-replicates:

所有站点之间会复制哪些内容
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. include:: /includes/common-replication.rst
   :start-after: start-mc-admin-replicate-what-replicates
   :end-before: end-mc-admin-replicate-what-replicates

哪些内容不会在站点之间复制
~~~~~~~~~~~~~~~~~~~~~~~~~~

.. include:: /includes/common-replication.rst
   :start-after: start-mc-admin-replicate-what-does-not-replicate
   :end-before: end-mc-admin-replicate-what-does-not-replicate


站点复制的初始流程
~~~~~~~~~~~~~~~~~~

启用站点复制后，身份与访问管理（IAM）设置会按以下顺序同步：

.. tab-set::

   .. tab-item:: MinIO IDP

      #. 策略
      #. 用户账户（针对本地用户）
      #. 组
      #. Access Keys
         
         ``root`` 的 Access Keys 不会同步。

      #. 已同步用户账户的策略映射
      #. :ref:`Security Token Service (STS) users <minio-security-token-service>` 的策略映射

   .. tab-item:: OIDC

      #. 策略
      #. 与具有有效 :ref:`MinIO Policy <minio-policy>` 的 OIDC 账户关联的 Access Keys。``root`` 的 Access Keys 不会同步。
      #. 已同步用户账户的策略映射
      #. :ref:`Security Token Service (STS) users <minio-security-token-service>` 的策略映射

   .. tab-item:: LDAP

      #. 策略
      #. 组
      #. 与具有有效 :ref:`MinIO Policy <minio-policy>` 的 LDAP 账户关联的 Access Keys。``root`` 的 Access Keys 不会同步。
      #. 已同步用户账户的策略映射
      #. :ref:`Security Token Service (STS) users <minio-security-token-service>` 的策略映射

在对等站点之间完成初始数据同步后，MinIO 会持续在所有站点之间复制并同步任何站点上新发生的 :ref:`可复制数据 <minio-site-replication-what-replicates>`。

站点自愈
~~~~~~~~

站点复制配置中的任意 MinIO 部署，都可以从拥有该数据最新版本的对等站点重新同步受损的 :ref:`可复制数据 <minio-site-replication-what-replicates>`。

.. versionchanged:: RELEASE.2023-07-18T17-49-40Z

   站点复制操作最多重试三（3）次。
   
   对于重试三次后仍复制失败的操作，MinIO 会将其移出队列。
   :ref:`scanner <minio-concepts-scanner>` 会在稍后重新扫描这些受影响对象，并将其重新加入复制队列。

.. versionchanged:: RELEASE.2022-08-11T04-37-28Z

   执行任意 ``GET`` 或 ``HEAD`` API 方法时，失败或待处理的复制会自动重新入队。
   例如，某个站点恢复在线后，使用 :mc:`mc stat`、:mc:`mc cat` 或 :mc:`mc ls` 命令会触发自愈重新入队。

.. versionchanged:: RELEASE.2022-12-02T23-48-47Z

   如果某个站点因任何原因丢失数据，可使用 :mc-cmd:`mc admin replicate resync` 从另一个健康站点重新同步数据。
   这会启动一个主动进程来重新同步数据，而无需等待被动的 :ref:`MinIO scanner <minio-concepts-scanner>` 识别缺失数据。

.. include:: /includes/common/scanner.rst
   :start-after: start-scanner-speed-config
   :end-before: end-scanner-speed-config

同步复制与异步复制
~~~~~~~~~~~~~~~~~~

.. include:: /includes/common-replication.rst
   :start-after: start-replication-sync-vs-async
   :end-before: end-replication-sync-vs-async

MinIO 强烈建议使用默认的异步站点复制。
同步站点复制的性能高度依赖站点之间的延迟，较高的延迟可能导致更低的 PUT 性能和复制滞后。
要配置同步站点复制，请使用 :mc-cmd:`mc admin replicate update` 并带上 :mc-cmd:`~mc admin replicate update --mode` 选项。

代理到其他站点
~~~~~~~~~~~~~~

MinIO 对等站点可以将对象的 ``GET/HEAD`` 请求代理到其他对等站点，以检查该对象是否存在。
这样一来，即使某个站点正在自愈或落后于其他对等站点，仍然可以返回已持久化到其他站点的对象。

例如：

1. 客户端向 ``Site1`` 发起 ``GET("data/invoices/january.xls")``
2. ``Site1`` 无法定位该对象
3. ``Site1`` 将请求代理到 ``Site2``
4. ``Site2`` 返回请求对象的最新版本
5. ``Site1`` 将代理返回的对象响应给客户端

对于*不*包含唯一版本 ID 的 ``GET/HEAD`` 请求，代理请求会返回该对象在对等站点上的*最新*版本。
这可能导致取回对象的非当前版本，例如响应请求的对等站点本身也存在复制滞后时。

MinIO 不会代理 ``LIST``、``DELETE`` 和 ``PUT`` 操作。

前提条件
--------

先备份集群设置
~~~~~~~~~~~~~~

在配置站点复制之前，使用 :mc:`mc admin cluster bucket export` 和 :mc:`mc admin cluster iam export` 命令分别对存储桶元数据和 IAM 配置进行快照。
如果在配置站点复制过程中发生误配置，可以使用这些快照恢复存储桶/IAM 设置。

初始化时仅允许一个站点包含数据
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

在初始化时，只允许*一个*站点包含数据。
其他站点必须不包含任何存储桶和对象。

配置站点复制后，第一个部署上的所有数据都会复制到其他站点。

所有站点必须使用相同的 IDP
~~~~~~~~~~~~~~~~~~~~~~~~~~

所有站点都必须使用相同的 :ref:`Identity Provider <minio-authentication-and-identity-management>`。
站点复制支持内置的 MinIO IDP、OIDC 或 LDAP。

所有站点必须使用相同的 MinIO 服务端版本
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

所有站点都必须使用一致且匹配的 MinIO 服务端版本。
在 MinIO 服务端版本不匹配的站点之间配置复制，可能导致意外或不符合预期的复制行为。

还应确保用于配置复制的 :mc:`mc` 版本尽量与服务器版本保持接近。

访问同一个加密服务
~~~~~~~~~~~~~~~~~~

如果通过 Key Management Service (KMS) 使用 :ref:`SSE-S3 <minio-encryption-sse-s3>` 或 :ref:`SSE-KMS <minio-encryption-sse-kms>` 加密，则所有站点都必须能够访问同一个中心化 KMS 部署。

可以通过一个中心化 KES 服务器，或多个 KES 服务器（例如每个站点一个）连接到同一个受支持的中心化 :ref:`key vault server <minio-sse>` 来实现。

复制要求启用版本控制
~~~~~~~~~~~~~~~~~~~~

站点复制*要求*启用 :ref:`minio-bucket-versioning`，并会自动为所有新建存储桶启用该功能。
在站点复制部署中，不能禁用版本控制。

对于存储桶中被排除在版本控制之外的前缀，MinIO 无法复制其中的对象。

每个站点都应部署负载均衡器
~~~~~~~~~~~~~~~~~~~~~~~~~~

.. include:: /includes/common-replication.rst
   :start-after: start-mc-admin-replicate-load-balancing
   :end-before: end-mc-admin-replicate-load-balancing

从存储桶复制切换到站点复制
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

:ref:`存储桶复制 <minio-bucket-replication>` 与多站点复制是互斥的。
不能在同一组部署上同时使用这两种复制方式。

如果此前已经配置了存储桶复制，而现在希望改用站点复制，则在初始化站点复制时，必须先删除包含数据的那个部署上的全部存储桶复制规则。
可在命令行中使用 :mc:`mc replicate rm` 删除存储桶复制规则。

设置站点复制时，只允许一个站点包含数据。
其他所有站点都必须为空。

教程
----

.. _minio-configure-site-replication:

配置站点复制
~~~~~~~~~~~~

以下步骤将为三个 :ref:`分布式部署 <deploy-minio-distributed>` 创建一个新的站点复制配置。
其中一个站点包含 :ref:`可复制数据 <minio-site-replication-what-replicates>`。

这三个站点分别使用别名 ``minio1``、``minio2`` 和 ``minio3``，且仅 ``minio1`` 包含数据。

#. 使用相同的 :ref:`IDP <minio-authentication-and-identity-management>`，:ref:`部署 <deploy-minio-distributed>` 三个或更多彼此独立的 MinIO 站点

   从空站点开始，*或* 确保最多只有一个站点包含任何 :ref:`可复制数据 <minio-site-replication-what-replicates>`。

#. 为每个站点配置别名

   .. include:: /includes/common-replication.rst
      :start-after: start-mc-admin-replicate-load-balancing
      :end-before: end-mc-admin-replicate-load-balancing

   例如，对于三个 MinIO 站点，可以创建别名 ``minio1``、``minio2`` 和 ``minio3``。
   
   使用 :mc:`mc alias set` 定义管理该站点连接的负载均衡器主机名或 IP。

   .. code-block:: shell

      mc alias set minio1 https://minio1.example.com:9000 adminuser adminpassword
      mc alias set minio2 https://minio2.example.com:9000 adminuser adminpassword
      mc alias set minio3 https://minio3.example.com:9000 adminuser adminpassword
      
   或定义环境变量

   .. code-block:: shell
   
      export MC_HOST_minio1=https://adminuser:adminpassword@minio1.example.com
      export MC_HOST_minio2=https://adminuser:adminpassword@minio2.example.com
      export MC_HOST_minio3=https://adminuser:adminpassword@minio3.example.com

#. 添加站点复制配置

   .. code-block:: shell
   
      mc admin replicate add minio1 minio2 minio3

   如果所有站点均为空，则别名顺序无关紧要。
   如果其中一个站点包含任何 :ref:`可复制数据 <minio-site-replication-what-replicates>`，则必须将其列在第一位。

   最多只能有一个站点包含可复制数据。

#. 查询站点复制配置进行验证

   .. code-block:: shell
   
      mc admin replicate info minio1

   可以使用站点复制配置中任意对等站点的别名。

#. 查询站点复制状态，确认初始数据已复制到所有对等站点。

   .. code-block:: shell

      mc admin replicate status minio1

   可以使用站点复制配置中任意对等站点的别名。
   输出应表明所有 :ref:`可复制数据 <minio-site-replication-what-replicates>` 均已同步。

   输出可能类似如下：

   .. code-block:: shell

      Bucket replication status:
      ●  1/1 Buckets in sync

      Policy replication status:
      ●  5/5 Policies in sync

      User replication status:
      No Users present

      Group replication status:
      No Groups present

   有关检查站点复制的更多信息，请参阅 :ref:`站点复制状态教程 <minio-site-replication-status-tutorial>`。


.. _minio-expand-site-replication:

扩展站点复制
~~~~~~~~~~~~

可以向现有站点复制配置中添加更多站点。

新站点必须满足以下要求：

- 站点已完成部署，并可通过主机名或 IP 访问
- 与配置中的其他站点共享相同的 IDP 配置
- 使用与其他已配置站点相同的 root 用户凭证
- 不包含任何存储桶或对象数据

#. 按照上述要求部署新的 MinIO 对等站点

#. 为新站点配置别名

   .. include:: /includes/common-replication.rst
      :start-after: start-mc-admin-replicate-load-balancing
      :end-before: end-mc-admin-replicate-load-balancing

   要检查现有别名，请使用 :mc:`mc alias list`。

   使用 :mc:`mc alias set` 定义管理新站点连接的负载均衡器主机名或 IP。

   .. code-block:: shell

      mc alias set minio4 https://minio4.example.com:9000 adminuser adminpassword

   或定义环境变量

   .. code-block:: shell
   
      export MC_HOST_minio4=https://adminuser:adminpassword@minio4.example.com

#. 添加站点复制配置

   使用 :mc-cmd:`mc admin replicate add` 命令，将新的对等站点加入站点复制配置中。
   先指定*所有*现有对等站点的别名，再指定要添加的新站点别名。

   例如，以下命令将新的对等站点 ``minio4`` 添加到现有站点复制配置中，该配置已包含 ``minio1``、``minio2`` 和 ``minio3`` 三个站点。

   .. code-block:: shell
   
      mc admin replicate add minio1 minio2 minio3 minio4

   .. note::

      如果任一站点不可达或已永久丢失，则在使用新站点进行扩展前，必须先使用 :mc-cmd:`mc admin replicate rm` 移除不可达站点。

#. 查询站点复制配置进行验证

   .. code-block:: shell
   
      mc admin replicate info minio1

修改站点的端点
~~~~~~~~~~~~~~~~~~~

如果某个对等站点变更了主机名，可以修改复制配置以反映新的主机名。

#. 使用 :mc-cmd:`mc admin replicate info` 获取站点的 Deployment ID

   .. code-block:: shell

      mc admin replicate info <ALIAS>
   

#. 使用 :mc-cmd:`mc admin replicate update` 更新站点的端点

   .. code-block:: shell

      mc admin replicate update ALIAS --deployment-id [DEPLOYMENT-ID] --endpoint [NEW-ENDPOINT]

   将 [DEPLOYMENT-ID] 替换为要更新站点的 deployment ID。
   
   将 [NEW-ENDPOINT] 替换为该站点的新端点。

   .. include:: /includes/common-replication.rst
      :start-after: start-mc-admin-replicate-load-balancing
      :end-before: end-mc-admin-replicate-load-balancing

从复制中移除站点
~~~~~~~~~~~~~~~~

可以随时将某个站点从复制中移除。
后续也可以重新添加该站点，但必须先彻底清除该站点上的存储桶和对象数据。

使用 :mc-cmd:`mc admin replicate rm`：

.. code-block:: shell

   mc admin replicate rm ALIAS PEER_TO_REMOVE --force

- 将 ``ALIAS`` 替换为复制配置中任意对等站点的 :ref:`alias <alias>`。

- 将 ``PEER_TO_REMOVE`` 替换为要移除的对等站点别名。

站点复制配置中的所有健康对等站点都会自动更新，以移除指定的对等站点。

MinIO 要求使用 ``--force`` 标志，才能将该对等站点从站点复制配置中移除。

.. _minio-site-replication-status-tutorial:

查看复制状态
~~~~~~~~~~~~

MinIO 提供跨站点的用户、组、策略或存储桶复制信息。

汇总信息包括各类别中 **Synced** 和 **Failed** 项目的数量。

使用 :mc-cmd:`mc admin replicate status`：

.. code-block:: shell

   mc admin replicate status <ALIAS> --<flag> <value>

例如：

- ``mc admin replicate status minio3 --bucket images``

  显示 ``minio3`` 站点上 ``images`` 存储桶的复制状态。
  
  输出类似如下：

  .. code-block::
 
     ●  Bucket config replication summary for: images
 
     Bucket          | MINIO2          | MINIO3          | MINIO4         
     Tags            |                 |                 |                
     Policy          |                 |                 |                
     Quota           |                 |                 |                
     Retention       |                 |                 |                
     Encryption      |                 |                 |                
     Replication     | ✔               | ✔               | ✔        

- ``mc admin replicate status minio3 --all``

  显示 ``minio3`` 所属全部复制站点的复制状态摘要。

  输出类似如下：

  .. code-block::

     Bucket replication status:
     ●  1/1 Buckets in sync
    
     Policy replication status:
     ●  5/5 Policies in sync
    
     User replication status:
     ●  1/1 Users in sync
    
     Group replication status:
     ●  0/2 Groups in sync
    
     Group           | MINIO2          | MINIO3          | MINIO4         
     ittechs         | ✗  in-sync      |                 | ✗  in-sync    
     managers        | ✗  in-sync      |                 | ✗  in-sync    
 
