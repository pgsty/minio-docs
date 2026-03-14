.. _minio-mc-ilm-tier-update:

======================
``mc ilm tier update``
======================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc ilm tier update

.. versionchanged:: RELEASE.2022-12-24T15-21-38Z

   :mc-cmd:`mc ilm tier update` 替代 ``mc admin tier edit``。

描述
----

.. start-mc-ilm-tier-update-desc

:mc:`mc ilm tier update` 命令用于修改已配置的远程层。

.. end-mc-ilm-tier-update-desc

.. admonition:: 仅在 MinIO 部署上使用 ``mc admin``
   :class: note

   .. include:: /includes/facts-mc-admin.rst
      :start-after: start-minio-only
      :end-before: end-minio-only

支持的 S3 服务
~~~~~~~~~~~~~~~

:mc:`mc ilm tier` *仅* 支持以下兼容 S3 的服务作为对象分层的远程目标：

- MinIO
- Amazon S3
- Google Cloud Storage
- Azure Blob Storage

所需权限
~~~~~~~~

MinIO 要求在你创建生命周期管理规则的存储桶范围内，具备以下权限。

- :policy-action:`s3:PutLifecycleConfiguration`
- :policy-action:`s3:GetLifecycleConfiguration`

MinIO 还要求在集群上具备以下管理权限，以便为对象过渡生命周期管理规则创建远程层：

- :policy-action:`admin:SetTier`
- :policy-action:`admin:ListTier`

例如，以下策略授予在集群中任意存储桶上配置对象过渡生命周期管理规则的权限：

.. literalinclude:: /extra/examples/LifecycleManagementAdmin.json
   :language: json
   :class: copyable

过渡权限
++++++++

对象过渡生命周期管理规则在远程存储层上需要额外权限。具体而言，MinIO 要求远程层凭证提供读取、写入、列出和删除权限。

例如，如果远程存储层实现基于 AWS IAM 策略的访问控制，则以下策略提供对象在远程层之间迁入和迁出所需的权限：

.. literalinclude:: /extra/examples/LifecycleManagementUser.json
   :language: json
   :class: copyable

请修改 ``Resource`` 为 MinIO 执行对象分层所使用的存储桶。

关于配置用户和权限以支持 MinIO 分层的更完整信息，请参阅受支持分层目标的文档：

- :aws-docs:`Amazon S3 Permissions <service-authorization/latest/reference/list_amazons3.html#amazons3-actions-as-permissions>`
- `Google Cloud Storage Access Control <https://cloud.google.com/storage/docs/access-control>`__
- `Authorizing access to data in Azure storage <https://docs.microsoft.com/en-us/azure/storage/common/storage-auth?toc=/azure/storage/blobs/toc.json>`__


语法
----

.. tab-set::

   .. tab-item:: EXAMPLE

      以下示例会更新 ``myminio`` 部署上名为 ``S3TIER`` 的现有远程层凭证。

      .. code-block:: shell
         :class: copyable

          mc ilm tier update myminio S3TIER --access-key ACCESS_KEY --secret-key SECRET_KEY

      运行此命令后，``myminio`` 部署上的生命周期管理规则会使用该层的新凭证将对象过渡到远程位置。
      命令中未修改的选项会保留其现有配置。

   .. tab-item:: SYNTAX

      该命令的语法如下：

      .. code-block:: shell
         :class: copyable

         mc ilm tier update TARGET                         \
                            TIER_NAME                      \
                            [--account-key value]          \
                            [--access-key value]           \
                            [--az-sp-tenant-id value]      \
                            [--az-sp-client-id value]      \
                            [--az-sp-client-secret value]  \
                            [--secret-key value]           \
                            [--use-aws-role]               \
                            [--credentials-file value]

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~

该命令接受以下参数：

.. mc-cmd:: TARGET
   :required:

   已配置 MinIO 部署的 :mc-cmd:`alias <mc alias>`。

.. mc-cmd:: TIER_NAME
   :required:

   命令要修改的远程层名称。
   该值对应于创建远程层时指定的 :mc-cmd:`mc ilm tier add TIER_NAME`。

.. mc-cmd:: --access-key
   :optional:

   远程 S3 或 MinIO 层上某个用户的 access key。
   该用户必须具备在远程存储桶或存储桶前缀上执行 read/write/list/delete 操作的权限。

   此选项仅适用于 :mc-cmd:`~mc ilm tier add TIER_TYPE` 为 ``s3`` 或 ``minio`` 的远程存储层。
   对其他 ``TIER_TYPE``，此选项无效。

.. mc-cmd:: --secret-key
   :optional:

   远程 ``s3`` 或 ``minio`` 层上某个用户的 secret key。

   此选项仅适用于 :mc-cmd:`~mc ilm tier add TIER_TYPE` 为 ``s3`` 或 ``minio`` 的远程存储层。
   对其他 ``TIER_TYPE``，此选项无效。

.. mc-cmd:: --use-aws-role
   :optional:

   使用本地已配置的 :iam-docs:`AWS Role <id_roles.html>` 的访问权限。

   此选项仅在 :mc-cmd:`~mc ilm tier add TIER_TYPE` 为 ``s3`` 或 ``minio`` 时适用。
   对其他 ``TIER_TYPE`` 值，此选项无效。

.. mc-cmd:: --account-key
   :optional:

   远程 Azure 层上某个用户的 account key。

   **Azure 层类型必填**。

   使用此选项轮换与远程层关联的 :mc-cmd:`~mc ilm tier add --account-name` 的凭证。

   此选项仅适用于 :mc-cmd:`~mc ilm tier add TIER_TYPE` 为 ``azure`` 的远程存储层。
   对其他登录类型，此选项无效。

.. mc-cmd:: --az-sp-tenant-id
   :optional:

   .. versionadded:: mc RELEASE.2024-07-03T20-17-25Z

   Azure service principal account 的 Directory ID。

   此选项仅适用于 :mc-cmd:`~mc ilm tier add TIER_TYPE` 为 ``azure`` 的远程存储层。
   对其他登录类型，此选项无效。

.. mc-cmd:: --az-sp-client-id
   :optional:

   .. versionadded:: mc RELEASE.2024-07-03T20-17-25Z

   Azure service principal account 的 Client ID。

   需要 :mc-cmd:`~mc ilm tier update --az-sp-client-secret`。

   此选项仅适用于 :mc-cmd:`~mc ilm tier add TIER_TYPE` 为 ``azure`` 的远程存储层。
   对其他登录类型，此选项无效。

.. mc-cmd:: --az-sp-client-secret
   :optional:

   .. versionadded:: mc RELEASE.2024-07-03T20-17-25Z

   Azure service principal account 的 secret。

   需要 :mc-cmd:`~mc ilm tier update --az-sp-client-id`。

   此选项仅适用于 :mc-cmd:`~mc ilm tier add TIER_TYPE` 为 ``azure`` 的远程存储层。
   对其他登录类型，此选项无效。

.. mc-cmd:: --credentials-file
   :optional:

   **Google Cloud Storage 层类型必填**。

   远程 GCS 层上某个用户的凭证文件。
   该用户必须具备在远程存储桶或存储桶前缀上执行 read/write/list/delete 操作的权限。

   此选项仅适用于 :mc-cmd:`~mc ilm tier add TIER_TYPE` 为 ``gcs`` 的远程存储层。
   对其他登录类型，此选项无效。

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

轮换 S3 远程层凭证
~~~~~~~~~~~~~~~~~~

以下示例更新 ``myminio`` 部署上名为 ``S3TIER`` 的 S3 远程层凭证。

.. code-block:: shell
   :class: copyable

   mc ilm tier update myminio S3TIER --access-key ACCESS_KEY --secret-key SECRET_KEY

- 将 ``S3TIER`` 替换为你的 Amazon Simple Storage Solution 层名称。
- 将 ``ACCESS_KEY`` 替换为你的 S3 存储更新后的 access key。
- 将 ``SECRET_KEY`` 替换为上面提供的 access key 对应的更新后 secret key。

轮换 Azure Blob Storage 远程层凭证
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下示例更新 ``myminio`` 部署上名为 ``AXTIER`` 的 Azure 远程层凭证。

.. code-block:: shell
   :class: copyable

   mc ilm tier update myminio AZTIER --account-key ACCOUNT-KEY

- 将 ``AZTIER`` 替换为你的 Azure 层名称。
- 将 ``ACCOUNT-KEY`` 替换为你的 Azure 存储更新后的密钥。

轮换 Google Cloud Storage 远程层凭证
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下示例更新 ``myminio`` 部署上名为 ``GCSTIER`` 的 Google Cloud Storage 远程层凭证。

.. code-block:: shell
   :class: copyable

    mc ilm tier update myminio GCSTIER --credentials-file /path/to/credentials.json


- 将 ``GCSTIER`` 替换为你的 Google Cloud Storage 层名称。
- 将 ``/path/to/credentials.json`` 替换为用于访问远程存储的更新后凭证文件路径。


S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility

所需权限
--------

有关修改层所需的权限，请参阅父命令中的 :ref:`required permissions <minio-mc-ilm-tier-permissions>`。
