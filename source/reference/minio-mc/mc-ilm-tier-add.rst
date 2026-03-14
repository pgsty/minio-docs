.. _minio-mc-ilm-tier-add:

===================
``mc ilm tier add``
===================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc ilm tier add

.. versionchanged:: RELEASE.2022-12-24T15-21-38Z

   :mc:`mc ilm tier add` 替代了 ``mc admin tier add``。

描述
-----------

.. start-mc-ilm-tier-add-desc

:mc:`mc ilm tier add` 命令在受支持的存储服务上创建一个新的远程存储层。

.. end-mc-ilm-tier-add-desc

完整列表请参见 :ref:`对象转换 <minio-lifecycle-management-tiering>`。

支持的 S3 服务
~~~~~~~~~~~~~~~~~~~~~

:mc:`mc ilm tier add` *仅*支持以下 S3 兼容服务作为对象分层的远程目标：

- MinIO
- Amazon S3
- Google Cloud Storage
- Azure Blob Storage

权限
~~~~~~~~~~~

在为对象转换生命周期管理规则创建远程层的集群上，MinIO 需要以下管理权限：

- :policy-action:`admin:SetTier`
- :policy-action:`admin:ListTier`

例如，以下策略授予在集群中任意存储桶上配置对象转换生命周期管理规则的权限：

.. literalinclude:: /extra/examples/LifecycleManagementAdmin.json
   :language: json
   :class: copyable

语法
------

.. tab-set::

   .. tab-item:: EXAMPLE

      以下示例在 ``myminio`` 部署上创建一个名为 ``WARM-MINIO-TIER`` 的新远程层。
      该命令为位于主机名 ``https://warm-minio.com`` 的远程 MinIO 部署创建一个层。

      .. code-block:: shell
         :class: copyable

          mc ilm tier add minio myminio WARM-MINIO-TIER                     \
                                        --endpoint https://warm-minio.com   \
                                        --access-key ACCESSKEY              \
                                        --secret-key SECRETKEY              \
                                        --bucket mybucket                   \
                                        --prefix myprefix/

      ``myminio`` 部署上的生命周期管理规则可以使用该新层，将对象转换到远程位置 ``mybucket`` 存储桶中的 ``myprefix/`` 前缀下。

   .. tab-item:: SYNTAX

      命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc ilm tier add TIER_TYPE                    \
                         TARGET                       \
                         TIER_NAME                    \
                         --bucket value               \
                         [--endpoint string]          \
                         [--region string]            \
                         [--access-key value^]        \
                         [--secret-key value^]        \
                         [--use-aws-role^]            \
                         [--aws-role-arn^]            \
                         [--aws-web-identity-file^]   \
                         [--azure-sp-tenant-id^]      \
                         [--azure-sp-client-id^]      \
                         [--azure-sp-client-secret^]  \
                         [--account-name value^]      \
                         [--account-key value^]       \
                         [--credentials-file value^]  \
                         [--prefix value]             \
                         [--storage-class value]

      **^注意：**每个受支持的存储供应商使用不同的认证方式。
      用于认证的标志因存储供应商而异。
      详见下方 :mc-cmd:`~mc ilm tier add TIER_TYPE`。

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


参数
~~~~~~~~~~

该命令接受以下参数：

.. mc-cmd:: TIER_TYPE
   :required:

   MinIO 将对象转换到的云服务提供商存储后端（"Tier"）。
   指定以下受支持值之一：

   .. list-table::
      :stub-columns: 1
      :width: 100%
      :widths: 30 70

      * - ``minio``
        - 使用远程 MinIO 部署作为新 Tier 的存储后端。

          还需要指定以下参数：

          - :mc-cmd:`~mc ilm tier add --access-key`
          - :mc-cmd:`~mc ilm tier add --secret-key`

      * - ``s3``
        - 使用 AWS S3 作为新 Tier 的存储后端。

          还需要指定以下参数：

          - :mc-cmd:`~mc ilm tier add --access-key`
          - :mc-cmd:`~mc ilm tier add --secret-key`

      * - ``azure``
        - 使用 :abbr:`Azure (Microsoft Azure)` Blob Storage 作为新 Tier 的存储后端。

          还需要指定以下参数：

          - :mc-cmd:`~mc ilm tier add --account-name`
          - :mc-cmd:`~mc ilm tier add --account-key`

      * - ``gcs``
        - 使用 :abbr:`GCP (Google Cloud Platform)` Cloud Storage 作为新 Tier 的存储后端。

          还需要指定以下参数：

          - :mc-cmd:`~mc ilm tier add --credentials-file`

.. mc-cmd:: TARGET
   :required:

   命令在该已配置 MinIO 部署的 :mc-cmd:`alias <mc alias>` 上创建新的远程层。
   随后你可以使用 :mc:`mc ilm rule add` 并指定该新远程层来创建新规则。

.. mc-cmd:: TIER_NAME
   :required:

   与新远程层关联的名称。
   该名称**必须**在 MinIO 集群所有已配置层中唯一。

   你**必须**使用全大写指定 tier，例如 ``WARM_TIER``。

.. mc-cmd:: --endpoint
   :optional:

   S3 或 MinIO 存储的 URL endpoint。
   URL endpoint **必须**解析到 :mc-cmd:`~mc ilm tier add TIER_TYPE` 指定的提供方。

   对 ``s3`` 或 ``minio`` tier 类型为必需，对 ``azure`` 为可选。
   对 ``TIER_TYPE`` 的任何其他取值，该选项均无效。

.. mc-cmd:: --access-key
   :optional:

   远程 ``S3`` 或 ``minio`` tier 类型中某个用户的 access key。
   该用户必须具有对远程存储桶或存储桶前缀执行 read/write/list/delete 操作的权限。

   当 :mc-cmd:`~mc ilm tier add TIER_TYPE` 为 ``s3`` 或 ``minio`` 时必需。
   对 ``TIER_TYPE`` 的任何其他取值，该选项均无效。

.. mc-cmd:: --secret-key
   :optional:

   远程 ``s3`` 或 ``minio`` tier 类型中某个用户的 secret key。

   当 :mc-cmd:`~mc ilm tier add TIER_TYPE` 为 ``s3`` 或 ``minio`` 时必需。
   对 ``TIER_TYPE`` 的任何其他取值，该选项均无效。

.. mc-cmd:: --account-name
   :optional:

   用作远程存储资源的 :azure-docs:`Storage Account <storage/common/storage-account-overview>`。

   当 :mc-cmd:`~mc ilm tier add TIER_TYPE` 为 ``azure`` 时必需。
   对 ``TIER_TYPE`` 的任何其他取值，该选项均无效。

   MinIO *不*支持更改与 Azure 远程 tier 关联的存储账户名。
   Azure 存储后端绑定到存储账户，因此更改该值会更换存储后端，并导致无法访问已转换到原账户/后端的任何对象。

.. mc-cmd:: --account-key
   :optional:

   与远程 Azure tier 关联的 :mc-cmd:`~mc ilm tier add --account-name` 对应的共享 account key。

   该 account key 必须绑定具备所需 :ref:`权限 <minio-lifecycle-management-transition-to-azure-permissions-remote>` 的 Azure 策略。

   当 :mc-cmd:`~mc ilm tier add TIER_TYPE` 为 ``azure`` 时必需。
   对 ``TIER_TYPE`` 的任何其他取值，该选项均无效。

.. mc-cmd:: --credentials-file
   :optional:

   远程 Google Cloud Storage tier 中用户使用的 `credential file <https://cloud.google.com/docs/authentication/getting-started>`__。
   该用户必须具有对远程存储桶或存储桶前缀执行 read/write/list/delete 操作的权限。

   当 :mc-cmd:`~mc ilm tier add TIER_TYPE` 为 ``gcs`` 时必需。
   对 ``TIER_TYPE`` 的任何其他取值，该选项均无效。

.. mc-cmd:: --bucket
   :required:

   MinIO 将对象转换到的远程 tier 存储桶。

   对于 ``azure`` 远程 tier，该值对应 :azure-docs:`Container name <storage/blobs/storage-blobs-introduction#containers>`

.. mc-cmd:: --prefix
   :optional:

   MinIO 将对象转换到指定 :mc-cmd:`~mc ilm tier add --bucket` 的前缀路径。

   省略此字段可将对象转换到存储桶根路径。

.. mc-cmd:: --storage-class
   :optional:

   MinIO 应用于转换到远程存储桶对象的 storage class（在 Microsoft Azure 中称为 "access tier"）。

   应用于 MinIO 转换到远程存储桶对象的 storage class。
   MinIO 分层行为依赖远程存储在请求后立即返回对象（毫秒到秒级）。
   因此 MinIO *无法*支持需要 rehydration、等待周期或手动干预的远程存储。

   选择与 ``TIER_TYPE`` 对应的选项卡，查看各层支持的值：

   .. tab-set::

      .. tab-item:: minio

         - ``STANDARD`` *推荐*
         - ``REDUCED``

         更多信息请参见 :ref:`纠删码存储类 <minio-ec-storage-class>`。

      .. tab-item:: s3

         - ``STANDARD``
         - ``STANDARD-IA``
         - ``ONEZONE-IA``

         更多信息请参见 :s3-docs:`使用 Amazon S3 存储类 <storage-class-intro.html>`。

      .. tab-item:: gcs

         - ``STANDARD``
         - ``NEARLINE``
         - ``COLDLINE``

         更多信息请参见 :gcs-docs:`GCS 存储类 <storage-classes>`。

      .. tab-item:: azure

         - ``Hot``
         - ``Cool``

         更多信息请参见 :azure-docs:`Blob 数据的 Hot、Cool 和 Archive 访问层 <storage/blobs/access-tiers-overview.html>`。

   若省略，对象将使用远程存储桶定义的默认存储类。

.. mc-cmd:: --region
   :optional:

   指定 :mc-cmd:`~mc ilm tier add TIER_TYPE` 的 S3 后端区域，例如 ``us-west-1``。

   该选项仅在 :mc-cmd:`~mc ilm tier add TIER_TYPE` 为 ``s3`` 或 ``minio`` 时生效。
   对 ``TIER_TYPE`` 的任何其他取值，该选项均无效。

.. mc-cmd:: --use-aws-role
   :optional:

   使用本地配置的 :iam-docs:`AWS Role <id_roles.html>` 的访问权限。

   该选项仅在 :mc-cmd:`~mc ilm tier add TIER_TYPE` 为 ``s3`` 或 ``minio`` 时生效。
   对 ``TIER_TYPE`` 的任何其他取值，该选项均无效。

.. mc-cmd:: --aws-role-arn
   :optional:

   转换对象时要使用的 AWS S3 角色名称。

   该选项仅在 :mc-cmd:`~mc ilm tier add TIER_TYPE` 为 ``s3`` **且**源端是 Amazon EKS 上的 MinIO pod 时生效。

.. mc-cmd:: --aws-web-identity-file
   :optional:

   指定转换对象时使用的 web identity token 文件。

   该选项仅在 :mc-cmd:`~mc ilm tier add TIER_TYPE` 为 ``s3`` **且**源端是 Amazon EKS 上的 MinIO pod 时生效。

.. mc-cmd:: --azure-sp-tenant-id
   :optional:

   用于登录 Azure 存储的 `service principal account <https://learn.microsoft.com/en-us/cli/azure/azure-cli-sp-tutorial-1>`__ 的 Tenant ID。

   该选项仅在 :mc-cmd:`~mc ilm tier add TIER_TYPE` 为 ``azure`` 且使用 service principal 身份登录时生效。
   对 ``TIER_TYPE`` 的任何其他取值，该选项均无效。

.. mc-cmd:: --azure-sp-client-id
   :optional:

   用于登录 Azure 存储的 `service principal account <https://learn.microsoft.com/en-us/cli/azure/azure-cli-sp-tutorial-1>`__ 的 Client ID。

   该选项仅在 :mc-cmd:`~mc ilm tier add TIER_TYPE` 为 ``azure`` 且使用 service principal 身份登录时生效。
   对 ``TIER_TYPE`` 的任何其他取值，该选项均无效。

.. mc-cmd:: --azure-sp-client-secret
   :optional:

   用于登录 Azure 存储的 `service principal account <https://learn.microsoft.com/en-us/cli/azure/azure-cli-sp-tutorial-1>`__ 的 client secret。

   该选项仅在 :mc-cmd:`~mc ilm tier add TIER_TYPE` 为 ``azure`` 且使用 service principal 身份登录时生效。
   对 ``TIER_TYPE`` 的任何其他取值，该选项均无效。

全局标志
~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
--------

配置一个 Tier 以将对象转换到 MinIO 部署
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下示例在本地部署上创建一个新层，已配置规则可使用该层将对象转换到独立的远程 MinIO 部署。

.. code-block:: shell
   :class: copyable

   mc ilm tier add minio myminio WARM-MINIO-TIER --endpoint https://warm-minio.com \
        --access-key ACCESSKEY --secret-key SECRETKEY --bucket mybucket --prefix myprefix/

该命令在 ``myminio`` 部署上为 ``minio`` 类型远程存储创建一个名为 ``WARM-MINIO-TIER`` 的新层。

- 远程 MinIO 存储位于 ``https://warm-minio.com``。
- 命令中包含了一个对该存储桶及前缀具备 read、write、list 和 delete 权限用户的凭据。
- 该层会将对象转换到远程 MinIO 存储上的 ``mybucket`` 存储桶与 ``myprefix`` 前缀。

配置一个 Tier 以将对象转换到 Azure Blob Storage 位置
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下示例在本地部署上创建一个新层，已配置规则可使用该层将对象转换到 Azure Blob Storage。

.. code-block:: shell
   :class: copyable

   mc ilm tier add azure myminio AZTIER --account-name ACCOUNT-NAME --account-key ACCOUNT-KEY \
        --bucket myazurebucket --prefix myazureprefix/

该命令在 ``myminio`` 部署上为 ``azure`` 类型远程存储创建一个名为 ``AZTIER`` 的新层。

- 使用提供的 account name 和 key 访问远程 Azure 存储。
- 该层会将对象转换到 Azure 存储上的 ``myazurebucket`` 存储桶与 ``myazureprefix`` 前缀。

配置一个 Tier 以将对象转换到 Google Cloud Storage
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下示例在本地部署上创建一个新层，已配置规则可使用该层将对象转换到 Google Cloud Storage。

.. code-block:: shell
   :class: copyable

    mc ilm tier add gcs myminio GCSTIER --credentials-file /path/to/credentials.json \
        --bucket mygcsbucket  --prefix mygcsprefix/

该命令在 ``myminio`` 部署上为 ``gcs`` 类型远程存储创建一个名为 ``GCSTIER`` 的新层。

- 使用提供的 credentials file 访问远程 GCS 存储。
- 该层会将对象转换到 GCS 存储上的 ``mygcsbucket`` 存储桶与 ``mygcsprefix`` 前缀。

配置一个 Tier 以将对象转换到 Amazon Simple Storage Service (S3)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下示例在本地部署上创建一个新层，已配置规则可使用该层将对象转换到 S3 上的 STANDARD 存储。

.. code-block:: shell
   :class: copyable

    mc ilm tier add s3 myminio S3TIER --endpoint https://s3.amazonaws.com \
        --access-key ACCESSKEY --secret-key SECRETKEY --bucket mys3bucket --prefix mys3prefix/ \
        --storage-class "STANDARD" --region us-west-2

该命令在 ``myminio`` 部署上为 ``s3`` 类型远程存储创建一个名为 ``S3TIER`` 的新层。

- S3 存储位于提供的 endpoint。
- 使用提供的 access key 和 secret key 访问远程 S3 存储。
- 该层会将对象转换到 GCS 存储上的 ``mys3bucket`` 存储桶与 ``mys3prefix`` 前缀。
- 该层使用位于 ``us-west-2`` S3 区域的 S3 ``STANDARD`` 存储类。

S3 兼容性
~~~~~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility


所需权限
--------------------

有关添加 tier 所需的权限，请参见父命令中的 :ref:`required permissions <minio-mc-ilm-tier-permissions>`。
