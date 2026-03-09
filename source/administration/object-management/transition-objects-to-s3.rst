.. _minio-lifecycle-management-transition-to-s3:

===================================
将对象从 MinIO 迁移到 S3
===================================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

本页中的步骤将创建一条新的对象生命周期管理规则，用于将对象从 MinIO 存储桶迁移到 Amazon
Web Services S3 存储后端或兼容 S3 的服务上的远程存储层。该过程适用于这类场景：在经过一定时间周期或达到某个日历日期后，将对象分层到低成本存储或归档存储。

.. todo: diagram

要求
------------

安装并配置 ``mc``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

此过程使用 :mc:`mc` 在 MinIO 集群上执行操作。
请在一台能够同时访问源集群和目标集群网络的机器上安装 :mc:`mc`。
有关下载和安装 ``mc`` 的说明，请参见 ``mc`` :ref:`快速安装 <mc-install>`。

使用 :mc:`mc alias set` 命令为源 MinIO 集群创建别名。
创建别名时，需要为源集群和目标集群上的用户指定 access key。
指定的用户必须具备配置和应用迁移操作所需的 :ref:`权限
<minio-lifecycle-management-transition-to-s3-permissions>`。

.. _minio-lifecycle-management-transition-to-s3-permissions:

所需的 MinIO 权限
~~~~~~~~~~~~~~~~~~~~~~~~~~

MinIO 要求为要创建生命周期管理规则的一个或多个存储桶授予以下权限。

- :policy-action:`s3:PutLifecycleConfiguration`
- :policy-action:`s3:GetLifecycleConfiguration`

MinIO 还要求在为对象迁移生命周期管理规则创建远程层的集群上具备以下管理权限：

- :policy-action:`admin:SetTier`
- :policy-action:`admin:ListTier`

例如，以下策略授予在集群中任意存储桶上配置对象迁移生命周期管理规则的权限：

.. literalinclude:: /extra/examples/LifecycleManagementAdmin.json
   :language: json
   :class: copyable

.. _minio-lifecycle-management-transition-to-s3-permissions-remote:

所需的 S3 权限
~~~~~~~~~~~~~~~~~~~~~~~

对象迁移生命周期管理规则还要求远程存储层具备额外权限。
具体来说，MinIO 要求远程层凭证对远程存储桶具备读取、写入、列出和删除权限。

例如，以下策略提供了将对象迁入和迁出远程层所需的权限：

.. literalinclude:: /extra/examples/LifecycleManagementUser.json
   :language: json
   :class: copyable

请根据 MinIO 分层对象所使用的存储桶修改 ``Resource``。

有关如何配置所需权限的更完整说明，请参见 :aws-docs:`Amazon S3 Permissions
<service-authorization/latest/reference/list_amazons3.html#amazons3-actions-as-permissions>`
文档。

远程存储桶必须已存在
~~~~~~~~~~~~~~~~~~~~~~~~

在配置以该存储桶为目标的生命周期管理层或规则之前，先创建远程 S3 存储桶。

注意事项
--------------

生命周期管理对象扫描器
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

MinIO 使用 :ref:`scanner process <minio-concepts-scanner>` 根据所有已配置的生命周期管理规则检查对象。
如果由于高 IO 工作负载或系统资源有限导致扫描速度较慢，可能会延迟生命周期管理规则的应用。
更多信息请参见 :ref:`minio-lifecycle-management-scanner`。

对远程数据的独占访问
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. include:: /includes/common-minio-tiering.rst
   :start-after: start-transition-bucket-access-desc
   :end-before: end-transition-bucket-access-desc

远程数据的可用性
~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. include:: /includes/common-minio-tiering.rst
   :start-after: start-transition-data-loss-desc
   :end-before: end-transition-data-loss-desc

步骤
---------

1) 配置生命周期管理的用户账户和策略
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. |permissions| replace:: :ref:`权限 <minio-lifecycle-management-transition-to-s3-permissions>`

.. include:: /includes/common-minio-tiering.rst
   :start-after: start-create-transition-user-desc
   :end-before: end-create-transition-user-desc

2) 配置远程存储层
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc:`mc ilm tier add` 命令将 Amazon S3 服务添加为新的远程存储层：

.. code-block:: shell
   :class: copyable

   mc ilm tier add s3 TARGET TIER_NAME  \
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

   * - :mc-cmd:`TARGET <mc ilm tier add TARGET>`
     - 要在其上配置 S3 远程层的 MinIO 部署的 :mc:`alias <mc alias>`。

   * - :mc-cmd:`TIER_NAME <mc ilm tier add TIER_NAME>`
     - 与新建 S3 远程存储层关联的名称。请使用全大写形式，例如 ``S3_TIER``。
       下一步需要使用该值。

   * - :mc-cmd:`HOSTNAME <mc ilm tier add --endpoint>`
     - S3 存储后端的 URL 端点。

   * - :mc-cmd:`ACCESS_KEY <mc ilm tier add --access-key>`
     - MinIO 用于访问该存储桶的 S3 access key。
       该 access key *必须* 对应一个具备所需
       :ref:`权限
       <minio-lifecycle-management-transition-to-s3-permissions-remote>` 的 IAM 用户。

   * - :mc-cmd:`SECRET_KEY <mc ilm tier add --secret-key>`
     - 与指定 ``ACCESS_KEY`` 对应的 secret key。

   * - :mc-cmd:`BUCKET <mc ilm tier add --bucket>`
     - S3 存储后端上 MinIO 用于迁移对象的存储桶名称。

   * - :mc-cmd:`PREFIX <mc ilm tier add --prefix>`
     - MinIO 迁移对象时使用的可选存储桶前缀。

       MinIO 会将所有已迁移对象存储在指定 ``BUCKET`` 下，并使用每个部署唯一的前缀值。
       省略此参数时，将仅使用该唯一值在远程存储中隔离和组织数据。

       对于包含其他数据的远程存储层，包括来自其他 MinIO 部署的已迁移对象，MinIO 建议指定此可选前缀。
       该前缀应能清晰指向源 MinIO 部署，以便于执行诊断、维护或灾难恢复相关操作。

   * - :mc-cmd:`STORAGE_CLASS <mc ilm tier add --storage-class>`
     - MinIO 迁移对象时使用的 S3 storage class。

       MinIO 的分层行为依赖远程存储在收到请求后立即返回对象（毫秒到秒级）。
       因此，MinIO *不能* 支持需要 rehydration、等待周期或人工干预的远程存储。

       以下 S3 storage class 满足 MinIO 对远程层的要求：

       - ``STANDARD``
       - ``STANDARD-IA``
       - ``STANDARD-ONEZONE``

       省略此值将使用该存储桶的默认 storage class。
       指定此值会覆盖存储桶的 storage class。

       更多信息请参见 :s3-docs:`Using Amazon S3 storage classes <storage-class-intro.html>`。

   * - :mc-cmd:`REGION <mc ilm tier add --region>`
     - 指定 ``BUCKET`` 所在的 AWS S3 区域。
       如果 ``HOSTNAME`` 已包含区域信息，则可以安全地省略此选项。

3) 创建并应用迁移规则
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. include:: /includes/common-minio-tiering.rst
   :start-after: start-create-transition-rule-desc
   :end-before: end-create-transition-rule-desc


4) 验证迁移规则
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc:`mc ilm rule ls` 命令查看已配置的迁移规则：

.. code-block:: shell
   :class: copyable

   mc ilm rule ls ALIAS/PATH --transition

- 将 :mc-cmd:`ALIAS <mc ilm rule ls ALIAS>` 替换为 MinIO 部署的 :mc:`alias <mc alias>`。

- 将 :mc-cmd:`PATH <mc ilm rule ls ALIAS>` 替换为要获取其已配置生命周期管理规则的存储桶名称。
