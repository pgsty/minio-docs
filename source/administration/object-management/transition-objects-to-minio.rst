.. _minio-lifecycle-management-transition-to-minio:

=============================================
将对象迁移到远程 MinIO 部署
=============================================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

本页中的操作步骤用于创建新的对象生命周期管理规则，将对象从主 MinIO 部署上的某个存储桶迁移到远程 MinIO 部署上的某个存储桶。
该流程支持成本管理策略，例如将对象从使用 NVMe 存储的“热” MinIO 部署分层到使用 SSD 的“温” MinIO 部署。

.. todo: diagram

前提条件
--------

安装并配置 ``mc``
~~~~~~~~~~~~~~~~~

此过程使用 :mc:`mc` 对 MinIO 集群执行操作。
请在一台同时具备源集群和目标集群网络访问能力的机器上安装 :mc:`mc`。
有关下载和安装 ``mc`` 的说明，请参见 ``mc`` :ref:`安装快速开始 <mc-install>`。

使用 :mc:`mc alias set` 命令为源 MinIO 集群创建别名。
创建别名时，需要为源集群和目标集群上的用户指定 access key。
所指定的用户必须具备配置和应用迁移操作所需的 :ref:`权限
<minio-lifecycle-management-transition-to-minio-permissions>`。

.. _minio-lifecycle-management-transition-to-minio-permissions:

所需的源 MinIO 权限
~~~~~~~~~~~~~~~~~~~~~~~~

MinIO 要求对要为其创建生命周期管理规则的存储桶授予以下权限。

- :policy-action:`s3:PutLifecycleConfiguration`
- :policy-action:`s3:GetLifecycleConfiguration`

对于要在其中为对象迁移生命周期管理规则创建远程层的集群，MinIO 还要求具备以下管理权限：

- :policy-action:`admin:SetTier`
- :policy-action:`admin:ListTier`

例如，以下策略授予在该集群任意存储桶上配置对象迁移生命周期管理规则的权限：

.. literalinclude:: /extra/examples/LifecycleManagementAdmin.json
   :language: json
   :class: copyable

.. _minio-lifecycle-management-transition-to-minio-permissions-remote:

所需的远程 MinIO 权限
~~~~~~~~~~~~~~~~~~~~~~~~~~

对象迁移生命周期管理规则要求在远程存储层上具备额外权限。
具体而言，MinIO 要求远程层凭据对远程存储桶具备读取、写入、列出和删除权限。

例如，远程 MinIO 部署上的以下策略提供了将对象迁入和迁出远程层所需的权限：

.. literalinclude:: /extra/examples/LifecycleManagementUser.json
   :language: json
   :class: copyable

请修改 ``Resource``，使其指向 MinIO 要迁移对象进入的存储桶。

有关配置所需权限的更完整指导，请参见 :ref:`minio-policy` 文档。

远程存储桶必须已存在
~~~~~~~~~~~~~~~~~~~~

必须先创建远程存储桶，然后才能配置以该存储桶为目标的生命周期管理层或规则。

如果远程存储桶中包含现有数据，请使用 :mc-cmd:`prefix <mc ilm tier add --prefix>` 功能，将已迁移对象与该存储桶中的其他对象隔离开来。

注意事项
--------

生命周期管理对象扫描器
~~~~~~~~~~~~~~~~~~~~~~

MinIO 使用 :ref:`扫描器进程 <minio-concepts-scanner>` 根据所有已配置的生命周期管理规则检查对象。
高 IO 负载或系统资源受限导致的扫描变慢，可能会延迟生命周期管理规则的生效。

对远程数据的独占访问
~~~~~~~~~~~~~~~~~~~~

.. include:: /includes/common-minio-tiering.rst
   :start-after: start-transition-bucket-access-desc
   :end-before: end-transition-bucket-access-desc

远程数据的可用性
~~~~~~~~~~~~~~~~

.. include:: /includes/common-minio-tiering.rst
   :start-after: start-transition-data-loss-desc
   :end-before: end-transition-data-loss-desc

过程
----

1) 配置生命周期管理的用户账户和策略
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. |permissions| replace:: :ref:`permissions <minio-lifecycle-management-transition-to-minio-permissions>`

.. include:: /includes/common-minio-tiering.rst
   :start-after: start-create-transition-user-desc
   :end-before: end-create-transition-user-desc

2) 配置远程存储层
~~~~~~~~~~~~~~~~~

使用 :mc:`mc ilm tier add` 命令将远程 MinIO 部署添加为新的远程存储层：

.. code-block:: shell
   :class: copyable

   mc ilm tier add minio TARGET TIER_NAME  \
      --endpoint https://HOSTNAME       \
      --access-key ACCESS_KEY           \
      --secret-key SECRET_KEY           \
      --bucket BUCKET                   \
      --prefix PREFIX                   \
      --storage-class STORAGE_CLASS     \
      --region REGION 

上面的示例使用了以下参数：

.. list-table::
   :header-rows: 1
   :widths: 30 70
   :width: 100%

   * - 参数
     - 说明
   
   * - :mc-cmd:`ALIAS <mc ilm tier add TARGET>`
     - 要在其上配置 MinIO 远程层的 MinIO 部署 :mc:`别名 <mc alias>`。
   
   * - :mc-cmd:`TIER_NAME <mc ilm tier add TIER_NAME>`
     - 要关联到新 MinIO 远程存储层的名称。
       请使用全大写名称，例如 ``MINIO_WARM_TIER``。下一步需要使用该值。

   * - :mc-cmd:`HOSTNAME <mc ilm tier add --endpoint>`
     - MinIO 存储后端的 URL endpoint。

   * - :mc-cmd:`ACCESS_KEY <mc ilm tier add --access-key>`
     - MinIO 用于访问存储桶的 access key。
       该 access key *必须* 对应到具备所需
       :ref:`权限
       <minio-lifecycle-management-transition-to-minio-permissions-remote>`.

   * - :mc-cmd:`SECRET_KEY <mc ilm tier add --secret-key>`
     - 与指定 ``ACCESS_KEY`` 对应的 secret key。

   * - :mc-cmd:`BUCKET <mc ilm tier add --bucket>`
     - 远程 MinIO 部署上用于接收 ``SOURCE`` 迁移对象的存储桶名称。

   * - :mc-cmd:`PREFIX <mc ilm tier add --prefix>`
     - MinIO 迁移对象时使用的可选存储桶前缀。

       MinIO 会将所有已迁移对象存储到指定的 ``BUCKET`` 中，并放在部署唯一的前缀值之下。
       省略此参数时，将仅使用该前缀值在远程存储中隔离和组织数据。

       对于包含其他数据的远程存储层，包括来自其他 MinIO 部署的已迁移对象，MinIO 建议指定此可选前缀。
       该前缀应能清晰标识回源 MinIO 部署，以便开展诊断、维护或灾难恢复等运维工作。

   * - :mc-cmd:`STORAGE_CLASS <mc ilm tier add --storage-class>`
     - MinIO 对迁移到远程 MinIO 存储桶的对象应用的 :ref:`纠删码存储类 <minio-ec-storage-class>`。
       请指定以下受支持存储类之一：

       - ``STANDARD`` *推荐*
       - ``REDUCED``

   * - :mc-cmd:`REGION <mc ilm tier add --region>`
     - 指定 ``BUCKET`` 的 MinIO region。

       MinIO 部署通常不需要在安装时设置 region。
       只有在您为该部署显式设置了 ``MINIO_SITE_REGION`` 配置项时，才需要包含此选项。

3) 创建并应用迁移规则
~~~~~~~~~~~~~~~~~~~~~

.. include:: /includes/common-minio-tiering.rst
   :start-after: start-create-transition-rule-desc
   :end-before: end-create-transition-rule-desc


4) 验证迁移规则
~~~~~~~~~~~~~~~~~~

使用 :mc:`mc ilm rule ls` 命令检查已配置的迁移规则：

.. code-block:: shell
   :class: copyable

   mc ilm rule ls ALIAS/PATH --transition

- 将 :mc-cmd:`ALIAS <mc ilm rule ls ALIAS>` 替换为 MinIO 部署的 :mc:`别名 <mc alias>`。

- 将 :mc-cmd:`PATH <mc ilm rule ls ALIAS>` 替换为要获取其生命周期管理规则的存储桶名称。
