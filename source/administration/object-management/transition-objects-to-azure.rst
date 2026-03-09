.. _minio-lifecycle-management-transition-to-azure:

======================================
将对象从 MinIO 迁移到 Azure
======================================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

本页中的过程将创建一条新的对象生命周期管理规则，用于将 MinIO 存储桶中的对象迁移到
:abbr:`Azure (Microsoft Azure)` 存储后端上的远程存储层。该过程适用于如下场景：
在达到特定时间周期或日历日期后，将陈旧数据移动到低成本的公有云存储方案中。

.. todo: diagram

要求
----

安装并配置 ``mc``
~~~~~~~~~~~~~~~~~

该过程使用 :mc:`mc` 在 MinIO 集群上执行操作。
请将 :mc:`mc` 安装在一台同时具备源集群和目标集群网络访问能力的主机上。
有关下载和安装 ``mc`` 的说明，请参见 ``mc`` :ref:`安装快速开始 <mc-install>`。

使用 :mc:`mc alias set` 命令为源 MinIO 集群创建别名。
创建别名时，需要为源集群和目标集群上的用户指定访问密钥。
所指定的用户必须具备配置和应用迁移操作所需的 :ref:`权限
<minio-lifecycle-management-transition-to-azure-permissions>`。

.. _minio-lifecycle-management-transition-to-azure-permissions:

所需的 MinIO 权限
~~~~~~~~~~~~~~~~~

MinIO 要求在要创建生命周期管理规则的一个或多个存储桶范围内具备以下权限。

- :policy-action:`s3:PutLifecycleConfiguration`
- :policy-action:`s3:GetLifecycleConfiguration`

此外，在为对象迁移生命周期管理规则创建远程层的集群上，MinIO 还要求具备以下管理权限：

- :policy-action:`admin:SetTier`
- :policy-action:`admin:ListTier`

例如，以下策略授予在集群中任意存储桶上配置对象迁移生命周期管理规则的权限：

.. literalinclude:: /extra/examples/LifecycleManagementAdmin.json
   :language: json
   :class: copyable

.. _minio-lifecycle-management-transition-to-azure-permissions-remote:

所需的 Azure 权限
~~~~~~~~~~~~~~~~~

对象迁移生命周期管理规则要求在远程存储层上具备额外权限。
具体而言，MinIO 要求 :abbr:`Azure (Microsoft Azure)` 凭证对远程存储账户和容器具备读取、
写入、列出和删除权限。

有关配置所需权限的更完整说明，请参阅 `Azure RBAC
<https://docs.microsoft.com/en-us/azure/role-based-access-control/>`__
文档。

远程存储账户和容器必须预先存在
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

在将该资源配置为生命周期管理层或规则的目标之前，先创建远程
:azure-docs:`Azure 存储账户 <storage/common/storage-account-overview>` 和容器。
在 :azure-docs:`创建 Azure 存储账户 <storage/common/storage-account-create>` 时，请确保该存储账户对应
Standard 或 Premium blob storage，并使用本地冗余存储（LRS）选项。
MinIO 使用的 Azure Go SDK API 不支持其他任何冗余选项。

如果您为存储账户设置了 :azure-docs:`默认访问层 <storage/blobs/access-tiers-online-manage>`，那么在定义远程层时，如果未指定 :mc-cmd:`storage class <mc ilm tier add --storage-class>`，MinIO 将使用该默认值。
请确保记录 Azure 存储账户和 MinIO 分层配置的相关设置，以避免潜在的混淆、误配置或其他意外结果。

有关 Azure 存储账户的更多信息，请参见 :azure-docs:`Storage accounts <storage/common/storage-account-overview#types-of-storage-accounts>`。

注意事项
--------

对远程数据的独占访问
~~~~~~~~~~~~~~~~~~~~

.. include:: /includes/common-minio-tiering.rst
   :start-after: start-transition-bucket-access-desc
   :end-before: end-transition-bucket-access-desc

.. important::

   MinIO *不* 支持更改与 Azure 远程层关联的账户名称。
   Azure 存储后端与该账户绑定，因此更改账户会改变存储后端，并导致无法访问已迁移到原始账户/后端的任何对象。

   如果您需要与 Azure 远程层配置相关的场景化指导，请联系 `MinIO Support <https://min.io/pricing?ref=docs>`__。

远程数据的可用性
~~~~~~~~~~~~~~~~

.. include:: /includes/common-minio-tiering.rst
   :start-after: start-transition-data-loss-desc
   :end-before: end-transition-data-loss-desc

过程
----

1) 为生命周期管理配置用户账户和策略
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. |permissions| replace:: :ref:`permissions <minio-lifecycle-management-transition-to-azure-permissions>`

.. include:: /includes/common-minio-tiering.rst
   :start-after: start-create-transition-user-desc
   :end-before: end-create-transition-user-desc

2) 配置远程存储层
~~~~~~~~~~~~~~~~~

使用 :mc:`mc ilm tier add` 命令添加新的远程存储层：

.. code-block:: shell
   :class: copyable

   mc ilm tier add azure TARGET TIER_NAME \
      --account-name ACCOUNT \
      --account-key KEY \
      --bucket CONTAINER \
      --endpoint ENDPOINT \
      --prefix PREFIX \
      --storage-class STORAGE_CLASS


上述示例使用了以下参数：

.. list-table::
   :header-rows: 1
   :widths: 30 70
   :width: 100%

   * - 参数
     - 说明
   
   * - :mc-cmd:`TARGET <mc ilm tier add TARGET>`
     - 要在其上配置远程层的 MinIO 部署的 :mc:`alias <mc alias>`。
   
   * - :mc-cmd:`TIER_NAME <mc ilm tier add TIER_NAME>`
     - 为新的 :abbr:`Azure (Microsoft Azure)` blob 远程存储层指定的名称。
       请使用全大写名称，例如 ``AZURE_TIER``。
       下一步中将需要该值。

   * - :mc-cmd:`ACCOUNT <mc ilm tier add --account-name>`
     - 用作远程存储资源的 :azure-docs:`Storage Account <storage/common/storage-account-overview>`。

       创建该层后，无法更改此账户名称。

   * - :mc-cmd:`KEY <mc ilm tier add --account-key>`
     - 指定 ``ACCOUNT`` 对应的共享账户密钥。

       该账户密钥对应的 Azure 策略必须具备所需 :ref:`权限
       <minio-lifecycle-management-transition-to-azure-permissions-remote>`。

       更多信息请参见 :azure-docs:`Managing storage account access keys <storage/common/storage-account-keys-manage>`。

   * - :mc-cmd:`CONTAINER <mc ilm tier add --bucket>`
     - MinIO 将对象迁移到其上的 :abbr:`Azure (Microsoft Azure)` 存储后端中的容器名称。

   * - :mc-cmd:`ENDPOINT <mc ilm tier add --endpoint>`
     - （可选）MinIO 将对象迁移到其上的 Azure blob 存储后端的完整 URL。
       如果未指定，默认为 ``https://ACCOUNT.blob.core.windows.net``。

   * - :mc-cmd:`PREFIX <mc ilm tier add --prefix>`
     - MinIO 迁移对象时使用的可选容器前缀。

       MinIO 会将所有已迁移对象存储在指定 ``BUCKET`` 下，并使用部署唯一的前缀值。
       省略此参数时，仅使用该值在远程存储中隔离和组织数据。

       对于包含其他数据的远程存储层，包括来自其他 MinIO 部署的已迁移对象，MinIO 建议指定该可选前缀。
       此前缀应能清晰指向源 MinIO 部署，以便开展与诊断、维护或灾难恢复相关的操作。

   * - :mc-cmd:`STORAGE_CLASS <mc ilm tier add --storage-class>`
     - MinIO 应用于迁移到 Azure 容器中对象的 Azure 访问层。

       MinIO 的分层行为依赖远程存储在收到请求后立即返回对象（毫秒到秒级）。
       因此，MinIO *不能* 支持需要 rehydration、等待周期或人工干预的远程存储。

       以下 Azure 访问层满足 MinIO 对远程层的要求：

       - ``Hot``
       - ``Cool``

       更多信息请参见 :azure-docs:`Hot, cool, and archive access tiers for blob data <storage/blobs/access-tiers-overview.html>`。

3) 创建并应用迁移规则
~~~~~~~~~~~~~~~~~~~~~

.. include:: /includes/common-minio-tiering.rst
   :start-after: start-create-transition-rule-desc
   :end-before: end-create-transition-rule-desc


4) 验证迁移规则
~~~~~~~~~~~~~~~~~~

使用 :mc:`mc ilm rule ls` 命令查看已配置的迁移规则：

.. code-block:: shell
   :class: copyable

   mc ilm rule ls ALIAS/PATH --transition

- 将 :mc-cmd:`ALIAS <mc ilm rule ls ALIAS>` 替换为 MinIO 部署的 :mc:`alias <mc alias>`。

- 将 :mc-cmd:`PATH <mc ilm rule ls ALIAS>` 替换为要检索其已配置生命周期管理规则的存储桶名称。
