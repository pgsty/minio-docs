.. _minio-lifecycle-management-transition-to-gcs:

====================================
将对象从 MinIO 迁移到 GCS
====================================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

本页中的过程会创建一条新的对象生命周期管理规则，将对象从 MinIO 存储桶迁移到 Google
Cloud Storage 后端上的远程存储层。该过程适用于这类场景：在达到特定时间周期或日历日期后，
将陈旧数据迁移到低成本的公有云存储方案。

.. todo: diagram

要求
----

安装并配置 ``mc``
~~~~~~~~~~~~~~~~~

该过程使用 :mc:`mc` 在 MinIO 集群上执行操作。
请在一台可同时通过网络访问源集群和目标集群的机器上安装 :mc:`mc`。
有关下载和安装 ``mc`` 的说明，请参见 ``mc`` :ref:`Installation Quickstart <mc-install>`。

使用 :mc:`mc alias set` 命令为源 MinIO 集群创建别名。
创建别名时，需要为源集群和目标集群上的用户指定访问密钥。
指定的用户必须具备用于配置和应用迁移操作的 :ref:`权限
<minio-lifecycle-management-transition-to-gcs-permissions>`。

.. _minio-lifecycle-management-transition-to-gcs-permissions:

所需的 MinIO 权限
~~~~~~~~~~~~~~~~~

MinIO 要求在您为其创建生命周期管理规则的一个或多个存储桶范围内，具备以下权限。

- :policy-action:`s3:PutLifecycleConfiguration`
- :policy-action:`s3:GetLifecycleConfiguration`

此外，在为对象迁移生命周期管理规则创建远程层的集群上，MinIO 还要求具备以下管理权限：

- :policy-action:`admin:SetTier`
- :policy-action:`admin:ListTier`

例如，以下策略授予在集群中任意存储桶上配置对象迁移生命周期管理规则的权限：

.. literalinclude:: /extra/examples/LifecycleManagementAdmin.json
   :language: json
   :class: copyable

.. _minio-lifecycle-management-transition-to-gcs-permissions-remote:

所需的 GCS 权限
~~~~~~~~~~~~~~~

对象迁移生命周期管理规则要求在远程存储层上具备额外权限。
具体来说，MinIO 要求 :abbr:`GCS (Google Cloud Storage)` 凭证对远程存储桶具备读取、写入、列出和删除权限。

有关配置所需权限的更完整说明，请参见 `GCS IAM permissions
<https://cloud.google.com/storage/docs/access-control/iam-permissions>`__ 文档。

远程存储桶必须已存在
~~~~~~~~~~~~~~~~~~~~

在配置以该存储桶为目标的生命周期管理层或规则之前，请先创建远程 GCS 存储桶。

如果您设置了默认的 GCS :gcs-docs:`storage class <storage-classes>`，那么在定义远程层时，如果未指定 :mc-cmd:`storage class <mc ilm tier add --storage-class>`，MinIO 就会使用该默认值。
请确保记录您的 GCS 存储桶设置以及 MinIO 分层配置，以避免潜在的混淆、错误配置或其他意外结果。

注意事项
--------

生命周期管理对象扫描器
~~~~~~~~~~~~~~~~~~~~~~

MinIO 使用 :ref:`scanner process <minio-concepts-scanner>` 根据所有已配置的生命周期管理规则检查对象。
高 IO 工作负载或有限的系统资源导致的扫描缓慢，可能会延迟生命周期管理规则的应用。
更多信息请参见 :ref:`minio-lifecycle-management-scanner`。

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

.. |permissions| replace:: :ref:`permissions <minio-lifecycle-management-transition-to-gcs-permissions>`

.. include:: /includes/common-minio-tiering.rst
   :start-after: start-create-transition-user-desc
   :end-before: end-create-transition-user-desc

2) 配置远程存储层
~~~~~~~~~~~~~~~~~

使用 :mc:`mc ilm tier add` 命令将新的 Google Cloud Storage
服务添加为远程存储层：

.. code-block:: shell
   :class: copyable

   mc ilm tier add gcs TARGET TIER_NAME \
      --bucket BUCKET \
      --prefix PREFIX \
      --credentials-file CREDENTIALS \
      --storage-class STORAGE_CLASS

上述示例使用了以下参数：

.. list-table::
   :header-rows: 1
   :widths: 30 70
   :width: 100%

   * - 参数
     - 说明

   * - :mc-cmd:`TARGET <mc ilm tier add TARGET>`
     - 要在其上配置 :abbr:`GCS (Google Cloud Storage)` 远程层的 MinIO 部署的
       :mc:`alias <mc alias>`。
   
   * - :mc-cmd:`TIER_NAME <mc ilm tier add TIER_NAME>`
     - 与新 :abbr:`GCS (Google Cloud Storage)` 远程存储层关联的名称。
       请使用全大写指定该名称，例如 ``GCS_TIER``。
       下一步需要使用该值。

   * - :mc-cmd:`BUCKET <mc ilm tier add --bucket>`
     - MinIO 迁移对象所使用的 :abbr:`GCS (Google Cloud Storage)` 存储后端上的存储桶名称。

   * - :mc-cmd:`PREFIX <mc ilm tier add --prefix>`
     - MinIO 迁移对象时使用的可选存储桶前缀。

       MinIO 会将所有已迁移对象存储到指定的 ``BUCKET`` 中，并使用每个部署唯一的前缀值。
       省略此参数时，MinIO 仅使用该值在远程存储中隔离和组织数据。

       对于包含其他数据（包括来自其他 MinIO 部署的已迁移对象）的远程存储层，
       MinIO 建议指定此可选前缀。该前缀应能清晰映射到源 MinIO 部署，
       以便简化与诊断、维护或灾难恢复相关的运维工作。

   * - :mc-cmd:`CREDENTIALS <mc ilm tier add --credentials-file>`
     - 远程 GCS 层上用户的 `credential file
       <https://cloud.google.com/docs/authentication/getting-started>`__。
       指定的用户凭证必须对应一个具备所需
       :ref:`权限
       <minio-lifecycle-management-transition-to-gcs-permissions-remote>` 的 GCS 用户。

   * - :mc-cmd:`STORAGE_CLASS <mc ilm tier add --storage-class>`
     - MinIO 应用于迁移到 GCS 存储桶中的对象的 :abbr:`GCS (Google Cloud Storage)` 存储类别。

       MinIO 的分层行为依赖于远程存储在收到请求后立即返回对象（毫秒到秒级）。
       因此，MinIO 无法支持需要回温、等待周期或人工干预的远程存储。

       以下 GCS 存储类别满足 MinIO 作为远程层的要求：

       - ``STANDARD``
       - ``NEARLINE``
       - ``COLDLINE``

       更多信息请参见 :gcs-docs:`GCS storage class <storage-classes>`。


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

- 将 :mc-cmd:`PATH <mc ilm rule ls ALIAS>` 替换为要获取其已配置生命周期管理规则的存储桶名称。
