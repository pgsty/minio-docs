.. _minio-mc-replicate-add:

====================
``mc replicate add``
====================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc replicate add

.. versionchanged:: RELEASE.2022-12-24T15-21-38Z 

   ``mc replicate add`` 替代了 ``mc admin bucket remote add`` 命令。

   MinIO 会根据给定的文件路径或资源位置（例如 IP 或 DNS 地址）自动创建远程目标。
   定义远程目标的用户不再需要为远程存储桶确定 ARN。

语法
------

.. start-mc-replicate-add-desc

:mc:`mc replicate add` 命令会为 MinIO 部署中的存储桶创建一条新的 :ref:`服务端复制
<minio-bucket-replication-serverside>` 规则。

.. end-mc-replicate-add-desc

远程存储桶**必须**位于与本地部署运行相同 MinIO 版本的 MinIO 部署上。

.. note::

   :mc:`mc mirror` 仅同步对象的当前版本，而 ``mc replicate`` 会同步对象的所有版本、版本信息和元数据。

创建规则后，MinIO 部署会自动开始将新对象同步到远程 MinIO 部署。
你也可以选择配置对现有对象、删除操作和已完全删除对象的同步。

.. tab-set::

   .. tab-item:: 示例

      以下命令为 ``myminio`` MinIO 部署上的 ``mydata`` 存储桶添加一条新的复制规则：

      .. code-block:: shell
         :class: copyable

         mc replicate add                                                     \
            --remote-bucket https://user:secret@minio.mysite.tld:9001/bucket  \
            --replicate "delete,delete-marker,existing-objects"               \
            myminio/mydata

      该复制规则会将带版本的删除操作、删除标记和现有对象同步到远程 MinIO 部署。

      .. versionchanged:: mc RELEASE.2024-03-03T00-13-08Z

         你可以在 ``--remote-bucket`` 参数中使用已配置的 ALIAS。

   .. tab-item:: 语法

      该命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] replicate add                     \
                          --remote-bucket string          \
                          [--bandwidth "string"]            \
                          [--disable]                       \
                          [--disable-proxy]                 \
                          [--healthcheck-seconds integer]   \
                          [--id "string"]                   \
                          [--limit-upload "string"]         \
                          [--limit-download "string"]       \
                          [--path "string"]                 \
                          [--region "string"]               \
                          [--replicate "string"]            \
                          [--storage-class "string"]        \
                          [--sync]                          \
                          [--tags "string"]                 \
                          [--priority int]                  \
                          ALIAS

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~~~~~~~

.. mc-cmd:: ALIAS
   :required:

   MinIO 部署的 :ref:`alias <alias>`，以及要在其上创建复制规则的存储桶或存储桶前缀的完整路径。
   例如：

   .. code-block:: none

      mc replicate add --remote-bucket https://user:secret@myminio.cloudprovider.tld:9001/bucket play/mybucket

.. mc-cmd:: --remote-bucket
   :required:

   .. versionchanged:: mc RELEASE.2024-03-03T00-13-08Z

      ``--remote-bucket`` 支持指定已有的 :ref:`alias <alias>`。

   指定远程位置的凭证、目标部署和存储桶。
   该值可以是 IP 地址、URL 或 :ref:`alias <alias>`/bucket。

   例如，基于 URL 的目标可能如下所示：

   .. code-block::

      https://user:secret@myminio.cloudprovider.tld:9001/bucket

   基于 alias 的目标可能如下所示：

   .. code-block::

      --remote-bucket minio-target/my-bucket

.. mc-cmd:: --bandwidth
   :optional:

   将带宽速率限制为不超过指定值，单位可为 KiB/s、MiB/s 或 GiB/s。
   有效单位包括：
   
   - ``B`` 表示 bytes
   - ``K`` 表示 kilobytes
   - ``G`` 表示 gigabytes
   - ``T`` 表示 terabytes
   - ``Ki`` 表示 kibibytes
   - ``Gi`` 表示 gibibytes
   - ``Ti`` 表示 tebibytes

   例如，要将带宽速率限制为不超过 1 GiB/s，可使用以下参数：

   .. code-block::

      --limit-upload 1Gi

   若未指定，MinIO 不会限制带宽速率。

    
.. mc-cmd:: --disable
   :optional:

   以“disabled”状态创建复制规则。
   在使用 :mc:`mc replicate update` 将其启用之前，MinIO 不会按该规则开始复制对象。

   在复制禁用期间创建的对象，在启用规则后不会立即具备复制资格。
   你必须在传给 :mc-cmd:`mc replicate update --replicate` 的复制功能列表中包含 ``"existing-objects"``，以显式启用现有对象复制。
   更多信息请参见 :ref:`minio-replication-behavior-existing-objects`。

.. mc-cmd:: --disable-proxy
   :optional:

   在定义存储桶之间的 active-active 复制时，不使用代理。

   默认情况下，MinIO 使用代理。

.. mc-cmd:: --healthcheck-seconds
   :optional:

   两次远程存储桶健康检查之间的时间间隔（秒）。

   若未指定，MinIO 使用 60 秒间隔。

.. mc-cmd:: --id
   :optional:

   为复制规则指定唯一 ID。
   如果未指定，MinIO 会自动生成一个 ID。

.. mc-cmd:: --limit-download
   :optional:

   将下载速率限制为不超过指定值，单位可为 KiB/s、MiB/s 或 GiB/s。
   有效单位包括：
   
   - ``B`` 表示 bytes
   - ``K`` 表示 kilobytes
   - ``G`` 表示 gigabytes
   - ``T`` 表示 terabytes
   - ``Ki`` 表示 kibibytes
   - ``Gi`` 表示 gibibytes
   - ``Ti`` 表示 tebibytes

   例如，要将下载速率限制为不超过 1 GiB/s，可使用以下参数：

   .. code-block::

      --limit-download 1G

   若未指定，MinIO 使用不限速的下载速率。

.. mc-cmd:: --limit-upload
   :optional:

   将上传速率限制为不超过指定值，单位可为 KiB/s、MiB/s 或 GiB/s。
   有效单位包括：
   
   - ``B`` 表示 bytes
   - ``K`` 表示 kilobytes
   - ``G`` 表示 gigabytes
   - ``T`` 表示 terabytes
   - ``Ki`` 表示 kibibytes
   - ``Gi`` 表示 gibibytes
   - ``Ti`` 表示 tebibytes

   例如，要将上传速率限制为不超过 1 GiB/s，可使用以下参数：

   .. code-block::

      --limit-upload 1G

   若未指定，MinIO 使用不限速的上传速率。


.. mc-cmd:: --path
   :optional:

   为远程存储桶启用 path-style 查找支持。

   有效值包括：

   - ``on`` - 使用路径查找来定位远程存储桶
   - ``off`` - 使用资源定位符风格（如域名或 IP 地址）查找来定位远程存储桶
   - ``auto`` - 由 MinIO 自动识别用于定位远程存储桶的正确查找类型

   未定义时，MinIO 使用 ``auto`` 值。

.. mc-cmd:: --priority
   :optional:

   指定复制规则的整数优先级。
   该值*必须*在源存储桶的所有其他规则中保持唯一。
   值越高表示优先级*越高*。

   默认值为 ``0``。

.. mc-cmd:: --region
   :optional:

   要将内容复制到的目标存储桶区域。

.. mc-cmd:: --replicate
   :optional:

   指定一个由逗号分隔的值列表，以启用扩展复制功能。

   - ``delete`` - 指示 MinIO 将 :ref:`DELETE 操作 <minio-object-delete>` 复制到目标存储桶。

   - ``delete-marker`` - 指示 MinIO 将删除标记复制到目标存储桶。

   - ``existing-objects`` - 指示 MinIO 复制在启用复制前创建的对象，或在复制暂停期间创建的对象。

   - ``metadata-sync`` - 指示 MinIO 为每个对象复制元数据。
     仅适用于 active-active 复制场景。

     省略此值会使 MinIO 停止将仅元数据变更回传到源端。

   若未指定，MinIO 会同步所有选项。

.. mc-cmd:: --storage-class
   :optional:

   指定应用到复制对象上的 MinIO :ref:`storage class <minio-ec-storage-class>`。

.. mc-cmd:: --sync
   :optional:

   为该远程目标启用同步复制。

   默认情况下，MinIO 使用异步复制。

.. mc-cmd:: --tags
   :optional:

   指定一个或多个以和号 ``&`` 分隔的键值对标签，MinIO 用其筛选要复制的对象。
   例如：

   .. code-block:: shell

      mc replicate add --tags "TAG1=VALUE&TAG2=VALUE&TAG3=VALUE" ALIAS

   MinIO 会将复制规则应用到标签集合
   包含指定复制标签的任何对象。


全局参数
~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
--------

配置存储桶复制
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下 :mc:`mc replicate add` 命令会创建一个复制配置，将所有新对象、现有对象、删除操作和删除标记同步到远程目标：

.. code-block:: shell
   :class: copyable

   mc replicate add myminio/mybucket \
      --remote-bucket https://user:secret@minio.mysite.tld/remotebucket \
      --replicate "delete,delete-marker,existing-objects"

- 将 ``myminio/mybucket`` 替换为 :mc-cmd:`~mc replicate add ALIAS` 及要创建复制配置的完整存储桶路径。

- 将 :mc-cmd:`~mc replicate add --remote-bucket` 的值替换为远程目标的 URL 或路径。
  如果使用文件路径格式的位置，请使用 ``--path on`` 选项。

- :mc-cmd:`~mc replicate add --replicate` 参数会指示 MinIO 将所有删除操作、删除标记和现有对象复制到远程端。
  关于复制行为的更多信息，请参见 :ref:`minio-replication-behavior-delete` 和 :ref:`minio-replication-behavior-existing-objects`。

为历史数据记录配置存储桶复制
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下 :mc:`mc replicate add` 命令会创建一个新的存储桶复制配置，将所有新对象和现有对象同步到远程目标：

.. code-block:: shell
   :class: copyable

   mc replicate add myminio/mybucket \
      --remote-bucket https://user:secret@minio.mysite.tld/remotebucket \
      --replicate "existing-objects"

- 将 ``myminio/mybucket`` 替换为 :mc-cmd:`~mc replicate add ALIAS` 及要创建复制配置的完整存储桶路径。

- 将 :mc-cmd:`~mc replicate add --remote-bucket` 的值替换为远程目标的位置。
  如果使用文件路径格式的位置，请使用 ``--path on`` 选项。

- :mc-cmd:`~mc replicate add --replicate` 参数会指示 MinIO 将所有现有对象复制到远程端。
  关于复制行为的更多信息，请参见 :ref:`minio-replication-behavior-existing-objects`。

生成的远程副本表示远程端对象的历史记录，源端的删除操作不会影响该远程副本。

行为
--------

服务端复制要求源端和目标端均为 MinIO
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

MinIO 服务端复制仅适用于 MinIO 部署之间。
源端和目标端部署都*必须*运行 MinIO。

若要在任意兼容 S3 的服务之间配置复制，请使用 :mc:`mc mirror`。

在源存储桶和目标存储桶上启用版本控制
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

MinIO 依赖版本控制提供的不可变性保护，在源端与复制目标之间同步对象。

在开始此流程前，使用 :mc:`mc version enable` 命令在源存储桶和目标存储桶*两端*启用版本控制：

.. code-block:: shell
   :class: copyable

   mc version enable ALIAS/PATH

- 将 :mc-cmd:`ALIAS <mc version enable ALIAS>` 替换为 MinIO 部署的 :mc:`alias <mc alias>`。

- 将 :mc-cmd:`PATH <mc version enable ALIAS>` 替换为要启用版本控制的存储桶。

所需权限
~~~~~~~~~~~~~~~~~~~~

MinIO 强烈建议专门创建用于支持存储桶复制操作的用户。
关于向 MinIO 部署添加用户和策略的更完整文档，请参见 :mc:`mc admin user` 和 :mc:`mc admin policy`。

.. tab-set::

   .. tab-item:: 复制管理员

      以下策略提供在部署上配置并启用复制所需的权限。

      .. literalinclude:: /extra/examples/ReplicationAdminPolicy.json
         :class: copyable
         :language: json

      - ``"EnableRemoteBucketConfiguration"`` 语句授予创建远程目标以支持复制的权限。

      - ``"EnableReplicationRuleConfiguration"`` 语句授予在存储桶上创建复制规则的权限。
        ``"arn:aws:s3:::*`` 资源将复制权限应用到源部署上的*任意*存储桶。
        你可以按需将用户策略限制到特定存储桶。

      使用 :mc-cmd:`mc admin policy create` 将该策略添加到每个作为复制源的部署。
      使用 :mc-cmd:`mc admin user add` 在部署上创建用户，并使用 :mc-cmd:`mc admin policy attach` 将策略关联到该新用户。

   .. tab-item:: 复制远程用户

      以下策略提供将复制数据同步*到*该部署所需的权限。

      .. literalinclude:: /extra/examples/ReplicationRemoteUserPolicy.json
         :class: copyable
         :language: json

      - ``"EnableReplicationOnBucket"`` 语句授予远程目标检索存储桶级配置的权限，以支持在 MinIO 部署中*所有*存储桶上的复制操作。
        若要将策略限制到特定存储桶，请在 ``Resource`` 数组中按 ``"arn:aws:s3:::bucketName"`` 形式指定这些存储桶。

      - ``"EnableReplicatingDataIntoBucket"`` 语句授予远程目标将数据同步到 MinIO 部署中*任意*存储桶的权限。
        若要将策略限制到特定存储桶，请在 ``Resource`` 数组中按 ``"arn:aws:s3:::bucketName/*"`` 形式指定这些存储桶。

      使用 :mc-cmd:`mc admin policy create` 将该策略添加到每个作为复制目标的部署。
      使用 :mc-cmd:`mc admin user add` 在部署上创建用户，并使用 :mc-cmd:`mc admin policy attach` 将策略关联到该新用户。

现有对象复制
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

从 :mc:`mc` :minio-git:`RELEASE.2021-06-13T17-48-22Z <mc/releases/tag/RELEASE.2021-06-13T17-48-22Z>` 和 :mc:`minio` :minio-git:`RELEASE.2021-06-07T21-40-51Z <minio/releases/tag/RELEASE.2021-06-07T21-40-51Z>` 开始，MinIO 支持自动复制存储桶中的现有对象。
MinIO 的现有对象复制实现了类似 `AWS Replicating existing objects between S3 buckets <https://aws.amazon.com/blogs/storage/replicating-existing-objects-between-s3-buckets/>`__ 的能力，而无需联系技术支持的额外开销。

- 若要在创建新复制规则时启用现有对象复制，请在传给 :mc-cmd:`mc replicate add --replicate` 的复制功能列表中包含 ``"existing-objects"``。

- 若要为已有复制规则启用现有对象复制，请使用 :mc-cmd:`mc replicate add --replicate` 将 ``"existing-objects"`` 添加到现有复制功能列表中。
  编辑复制规则时，你必须指定*所有*期望的复制功能。

有关此行为的更完整文档，请参见 :ref:`minio-replication-behavior-existing-objects`。

元数据变更同步
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

MinIO 支持 :ref:`双向 active-active <minio-bucket-replication-serverside-twoway>` 复制配置，即在两个 MinIO 部署上的存储桶之间同步新增和修改后的对象。
从 :mc:`mc` :minio-git:`RELEASE.2021-05-18T03-39-44Z <mc/releases/tag/RELEASE.2021-05-18T03-39-44Z>` 开始，MinIO 默认会将复制对象的仅元数据变更同步回“源”部署。
在该更新之前，MinIO 不支持同步复制对象的仅元数据变更。

启用元数据同步后，MinIO 会重置对象的 :ref:`复制状态 <minio-replication-process>` 以表明其具备复制资格。
具体而言，当应用对状态为 ``REPLICA`` 的对象执行仅元数据更新时，MinIO 会将对象标记为 ``PENDING`` 并使其具备复制资格。

若要禁用元数据同步，请使用 :mc-cmd:`mc replicate update --replicate` 命令，并在复制功能列表中省略 ``replica-metadata-sync``。

删除操作复制
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

MinIO 支持将删除操作复制到目标存储桶。
具体来说，MinIO 可以同时复制 :s3-docs:`Delete Markers <versioning-workflows.html>` *以及* 特定版本对象的删除：

- 对对象执行删除操作时，MinIO 复制也会在目标存储桶创建删除标记。

- 对对象版本执行删除操作时，MinIO 复制也会删除目标存储桶中的对应版本。

对于因 :ref:`生命周期管理过期规则 <minio-lifecycle-management-expiration>` 被删除的对象，MinIO *不会*进行复制。
MinIO 仅复制由客户端显式触发的删除操作。

MinIO 要求使用 :mc-cmd:`mc replicate add --replicate` 参数显式启用删除操作复制。
本流程包含启用删除操作和删除标记复制所需的参数。
有关此行为的更完整文档，请参见 :ref:`minio-replication-behavior-delete`。

加密对象复制
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

MinIO 支持复制使用自动 Server-Side Encryption (SSE-S3) 加密的对象。
要复制加密对象，源存储桶和目标存储桶都*必须*启用自动 SSE-S3。

作为复制流程的一部分，MinIO 会在源存储桶上*解密*对象，并传输未加密对象。
目标 MinIO 部署随后会使用目标存储桶的 SSE-S3 配置重新加密该对象。
MinIO *强烈建议*在源端和目标端部署上都 :ref:`启用 TLS <minio-TLS>`，以确保对象在传输过程中的安全。

MinIO *不*支持复制客户端侧加密对象（SSE-C）。

S3 兼容性
~~~~~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
