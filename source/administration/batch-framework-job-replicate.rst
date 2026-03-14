.. _minio-batch-framework-replicate-job:

============
批量复制
============

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. versionadded:: MinIO RELEASE.2022-10-08T20-11-00Z

   批处理框架在 :mc:`mc` :mc-release:`RELEASE.2022-10-08T20-11-00Z` 中随 ``replicate`` 作业类型一同引入。

MinIO 批处理框架允许您使用 YAML 格式的作业定义文件（“批处理文件”）来创建、管理、监控和执行作业。
批处理作业直接在 MinIO 部署上运行，可利用服务端处理能力，而不受运行 :ref:`MinIO Client <minio-client>` 的本地机器限制。

``replicate`` 批处理作业会将对象从一个 MinIO 部署（``source`` 部署）复制到另一个 MinIO 部署（``target`` 部署）。
``source`` 或 ``target`` 中**必须**有一方是 :ref:`local <minio-batch-local>` 部署。

与使用 :mc:`mc mirror` 相比，MinIO 部署之间的批量复制具有以下优势：

- 消除了客户端到集群网络成为潜在瓶颈的可能
- 用户只需要具备启动批处理作业的访问权限，而无需其他权限，因为作业完全在集群服务端运行
- 如果对象未能复制，作业可提供重试机制
- 批处理作业是一次性的、可编排的过程，可对复制进行精细控制
- （仅限 MinIO 到 MinIO）复制过程会将对象版本从源端复制到目标端

从 MinIO Server ``RELEASE.2023-05-04T21-44-30Z`` 开始，另一端部署既可以是另一个 MinIO 部署，也可以是使用实时存储类的任意 S3 兼容位置。
使用复制 ``YAML`` 文件中的筛选选项，可排除存储在需要先重新水化或通过其他恢复方式处理后才能提供请求对象的位置中的对象。
复制到此类远端时，批量复制采用 ``mc mirror`` 的行为。

行为
----

访问控制与要求
~~~~~~~~~~~~~~

批量复制与 :ref:`bucket replication <minio-bucket-replication-requirements>` 具有类似的访问和权限要求。

“source” 部署所使用的凭据必须具有类似以下的策略：

.. literalinclude:: /extra/examples/ReplicationAdminPolicy.json
   :class: copyable
   :language: json

“remote” 部署所使用的凭据必须具有类似以下的策略：

.. literalinclude:: /extra/examples/ReplicationRemoteUserPolicy.json
   :class: copyable
   :language: json


有关向 MinIO 部署添加用户、访问密钥和策略的更完整文档，请参见 :mc:`mc admin user`、:mc:`mc admin user svcacct` 和 :mc:`mc admin policy`。

已配置 :ref:`Active Directory/LDAP <minio-external-identity-management-ad-ldap>` 或 :ref:`OpenID Connect <minio-external-identity-management-openid>` 用户管理的 MinIO 部署，也可以创建专用的 :ref:`access keys <minio-idp-service-account>` 来支持批量复制。

筛选复制目标
~~~~~~~~~~~~

批处理作业定义文件可按存储桶、前缀和/或筛选条件限制复制范围，从而只复制特定对象。
复制过程对对象和存储桶的访问，可能会受到您在 YAML 中为源端或目标端提供的凭据限制。

.. versionchanged:: MinIO Server RELEASE.2023-04-07T05-28-58Z

   您可以从远程 MinIO 部署复制到运行该批处理作业的本地部署。

例如，您可以使用批处理作业执行一次性复制同步，将对象从本地部署 ``minio-local/invoices/`` 上的某个存储桶推送到远程部署 ``minio-remote/invoices`` 上的某个存储桶。
您也可以将对象从远程部署 ``minio-remote/invoices`` 拉取到本地部署 ``minio-local/invoices``。

小文件优化
~~~~~~~~~~

从 :minio-release:`RELEASE.2023-12-09T18-17-51Z` 开始，批量复制默认会自动对小于 5MiB 的对象进行批量打包和压缩，以高效地在源端与远端之间传输数据。
远程 MinIO 部署可以检查这些打包后的对象，并立即应用生命周期管理分层规则。
该功能类似于 S3 Snowball Edge 提供的小文件批处理能力。

您可以在 :ref:`replicate <minio-batch-job-types>` 作业配置中修改压缩设置。

.. _minio-batch-framework-replicate-job-ref:

复制批处理作业参考
------------------

YAML **必须** 定义源部署和目标部署。
如果 *source* 部署是远程部署，则 *target* 部署 **必须** 为 ``local``。
此外，YAML 还可以定义标志，用于筛选要复制的对象、为作业发送通知或定义作业的重试次数。

.. versionchanged:: MinIO RELEASE.2023-04-07T05-28-58Z

   您可以从远程 MinIO 部署复制到运行该批处理作业的本地部署。

.. versionchanged:: MinIO RELEASE.2024-08-03T04-33-23Z

   此版本引入了 Batch Job Replicate API 的新版本 ``v2``。
   更新后的 API 允许您在源端列出多个要复制的前缀。
   若要从一个源端复制多个前缀，请将 ``replicate.apiVersion`` 指定为 ``v2``。

   .. code-block::
      :class: copyable

      replicate:
        apiVersion: v1
        source:
          type: minio
          bucket: mybucket
          prefix:
            - prefix1
            - prefix2
      ...

对于**源部署**

- 必需信息

  .. list-table::
     :widths: 25 75
     :width: 100%

     * - ``type:``
       - 必须为 ``minio``。
     * - ``bucket:``
       - 部署上的存储桶。

- 可选信息

  .. list-table::
     :widths: 25 75
     :width: 100%

     * - ``prefix:``
       - | 应复制对象的前缀。
         | 从 MinIO Server ``RELEASE.2024-08-03T04-33-23Z`` 开始，Batch Job Replicate API 的 v2 允许您列出多个前缀。
         | 将 ``replicate.apiVersion`` 指定为 ``v2``，即可从多个前缀执行复制。

     * - ``endpoint:``
       - | 用于复制批处理作业源端或目标端的部署位置。
         | 例如，``https://minio.example.net``。
         |
         | 如果该部署就是命令中指定的 :ref:`alias`，可省略此字段，让 MinIO 使用该别名对应的 endpoint 和 credentials 值。
         | 源部署或远程部署中*必须*有一方是 :ref:`"local" <minio-batch-local>` 别名。
         | 非 “local” 部署必须指定 ``endpoint`` 和 ``credentials``。

     * - ``path:``
       - | 指示 MinIO 使用存储桶的 Path 或 Virtual Style（DNS）查找方式。
         |
         | - 指定 ``on`` 使用 Path 风格
         | - 指定 ``off`` 使用 Virtual 风格
         | - 指定 ``auto`` 让 MinIO 自动判断正确的查找方式。
         |
         | 默认为 ``auto``。

     * - ``credentials:``
       - | 授予对象访问权限的 ``accesskey:`` 和 ``secretKey:``，或 ``sessionToken:``。
         | 仅对非 :ref:`local <minio-batch-local>` 部署指定此项。

     * - ``snowball``
       - | *版本新增*：RELEASE.2023-12-09T18-17-51Z
         |
         | 用于控制批量打包与压缩功能的配置选项。

     * - ``snowball.disable``
       - | 指定 ``true`` 可在复制期间禁用批量打包与压缩功能。
         | 默认为 ``false``。

     * - ``snowball.batch``
       - | 指定用于压缩打包的对象最大整数数量。
         | 默认为 ``100``。

     * - ``snowball.inmemory``
       - | 指定 ``false`` 表示使用本地存储暂存归档，或指定 ``true`` 表示暂存到内存（RAM）。
         | 默认为 ``true``。

     * - ``snowball.compress``
       - | 指定 ``true`` 可在线路传输中使用 `S2/Snappy compression algorithm <https://en.wikipedia.org/wiki/Snappy_(compression)>`__ 生成压缩后的打包对象。
         | 默认为 ``false``，即不压缩。

     * - ``snowball.smallerThan``
       - | 指定对象大小阈值，小于该值时 MinIO 应对对象执行批量打包。
         | 默认为 ``5MiB``。

     * - ``snowball.skipErrs``
       - | 指定 ``false`` 可让 MinIO 在读取任意产生错误的对象时停止处理。
         | 默认为 ``true``。

对于**目标部署**

- 必需信息

  .. list-table::
     :widths: 25 75
     :width: 100%

     * - ``type:``
       - 必须为 ``minio``。
     * - ``bucket:``
       - 部署上的存储桶。

- 可选信息

  .. list-table::
     :widths: 25 75
     :width: 100%

     * - ``prefix:``
       - 要复制对象的前缀。

     * - ``endpoint:``
       - | 目标部署的位置。
         |
         | 如果目标是命令中指定的 :ref:`alias <alias>`，则可以省略此项以及 ``credentials`` 字段。
         | 如果目标是 “local”，则源端*必须*通过 ``endpoint`` 和 ``credentials`` 指定远程部署。


     * - ``credentials:``
       - 授予对象访问权限的 ``accesskey`` 和 ``secretKey``，或 ``sessionToken``。

对于**筛选条件**

.. list-table::
   :widths: 25 75
   :width: 100%

   * - ``newerThan:``
     - 表示时长的字符串，格式为 ``#d#h#s``。

       仅复制比指定时长更新的对象。
       例如，``7d``、``24h``、``5d12h30s`` 都是有效字符串。
   * - ``olderThan:``
     - 表示时长的字符串，格式为 ``#d#h#s``。

       仅复制比指定时长更旧的对象。
   * - ``createdAfter:``
     - 采用 ``YYYY-MM-DDTHH:MM:SSZ`` :rfc:`RFC3339 <3339>` 日期时间格式的日期。

       仅复制在该日期之后创建的对象。
   * - ``createdBefore:``
     - 采用 ``YYYY-MM-DDTHH:MM:SSZ`` :rfc:`RFC3339 <3339>` 日期时间格式的日期。

       仅复制在该日期之前创建的对象。

对于**通知**

.. list-table::
   :widths: 25 75
   :width: 100%

   * - ``endpoint:``
     - 用于发送通知事件的预定义 endpoint。
   * - ``token:``
     - 用于访问 ``endpoint`` 的可选 :abbr:`JWT <JSON Web Token>`。

对于**重试次数**

如果有任何因素中断作业，您可以定义该批处理作业的重试次数。
对于每次重试，您还可以定义两次尝试之间的等待时长。

.. list-table::
   :widths: 25 75
   :width: 100%

   * - ``attempts:``
     - 在放弃之前完成该批处理作业的尝试次数。
   * - ``delay:``
     - 每次尝试之间至少等待的时间。

``replicate`` 作业类型的 YAML 描述文件示例
------------------------------------------

使用 :mc:`mc batch generate` 创建基础的 ``replicate`` 批处理作业，以便进一步自定义。

对于 :ref:`local <minio-batch-local>` 部署，请勿指定 endpoint 或 credentials。
根据哪一方是 ``local``，删除或注释掉源端或目标端部分中的这些行。

.. literalinclude:: /includes/code/replicate.yaml
   :language: yaml
