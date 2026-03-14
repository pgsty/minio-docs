===============
``mc ilm tier``
===============

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc ilm tier

.. versionchanged:: RELEASE.2022-12-24T15-21-38Z

   :mc-cmd:`mc ilm tier` replaces ``mc admin tier``.

说明
----

.. start-mc-ilm-tier-desc

:mc:`mc ilm tier` 命令及其子命令用于为 MinIO 的 :ref:`生命周期管理：对象过渡（“分层”）<minio-lifecycle-management-expiration>` 配置受支持的远程 S3 兼容服务。

.. end-mc-ilm-tier-desc

使用此命令创建一个或多个层后，可使用 :mc-cmd:`mc ilm rule` 及其子命令创建将对象迁移到其他存储的规则。

有关更多信息，请参阅 :ref:`生命周期管理 <minio-lifecycle-management>` 概述。

子命令
------

:mc-cmd:`mc ilm tier` 包含以下子命令：

.. list-table::
   :header-rows: 1
   :widths: 30 70
   :width: 100%

   * - 子命令
     - 说明

   * - :mc:`~mc ilm tier add`
     - .. include:: /reference/minio-mc/mc-ilm-tier-add.rst
          :start-after: start-mc-ilm-tier-add-desc
          :end-before: end-mc-ilm-tier-add-desc

   * - :mc:`~mc ilm tier check`
     - .. include:: /reference/minio-mc/mc-ilm-tier-check.rst
          :start-after: start-mc-ilm-tier-check-desc
          :end-before: end-mc-ilm-tier-check-desc

   * - :mc:`~mc ilm tier info`
     - .. include:: /reference/minio-mc/mc-ilm-tier-info.rst
          :start-after: start-mc-ilm-tier-info-desc
          :end-before: end-mc-ilm-tier-info-desc

   * - :mc:`~mc ilm tier ls`
     - .. include:: /reference/minio-mc/mc-ilm-tier-ls.rst
          :start-after: start-mc-ilm-tier-ls-desc
          :end-before: end-mc-ilm-tier-ls-desc

   * - :mc:`~mc ilm tier rm`
     - .. include:: /reference/minio-mc/mc-ilm-tier-rm.rst
          :start-after: start-mc-ilm-tier-rm-desc
          :end-before: end-mc-ilm-tier-rm-desc

   * - :mc:`~mc ilm tier update`
     - .. include:: /reference/minio-mc/mc-ilm-tier-update.rst
          :start-after: start-mc-ilm-tier-update-desc
          :end-before: end-mc-ilm-tier-update-desc


.. _minio-mc-ilm-tier-permissions:

所需权限
--------

要为对象过渡创建层，MinIO 要求在集群上具有以下管理权限：

- :policy-action:`admin:SetTier`
- :policy-action:`admin:ListTier`

例如，以下策略提供了足够的权限，可为集群中的任意存储桶配置对象过渡生命周期管理规则：

.. literalinclude:: /extra/examples/LifecycleManagementAdmin.json
   :language: json
   :class: copyable

过渡权限
~~~~~~~~

对象过渡生命周期管理规则要求远程存储层具备额外权限。
具体而言，MinIO 要求远程层凭证提供读取、写入、列出和删除权限。

例如，如果远程存储层使用基于 AWS IAM 策略的访问控制，则以下策略提供了对象迁入和迁出远程层所需的权限：

.. literalinclude:: /extra/examples/LifecycleManagementUser.json
   :language: json
   :class: copyable

请将 ``Resource`` 修改为 MinIO 用于对象分层的目标存储桶。

.. admonition:: 避免在远程层启用版本控制
   :class: important

   MinIO 强烈建议不要为远程层启用存储桶版本控制。
   如果远程层存储桶启用了版本控制，则每个源对象版本都会过渡为远程层中的一个*唯一对象*。
   
   如果你的环境要求远程层启用版本控制，则还必须允许 ``s3:DeleteObjectVersion`` 权限。

有关如何配置用户和权限以支持 MinIO 分层的更完整信息，请参阅受支持分层目标的相关文档：

- :aws-docs:`Amazon S3 Permissions <service-authorization/latest/reference/list_amazons3.html#amazons3-actions-as-permissions>`
- `Google Cloud Storage Access Control <https://cloud.google.com/storage/docs/access-control>`__
- `Authorizing access to data in Azure storage <https://docs.microsoft.com/en-us/azure/storage/common/storage-auth?toc=/azure/storage/blobs/toc.json>`__


.. toctree::
   :titlesonly:
   :hidden:
   
   /reference/minio-mc/mc-ilm-tier-add
   /reference/minio-mc/mc-ilm-tier-check
   /reference/minio-mc/mc-ilm-tier-info
   /reference/minio-mc/mc-ilm-tier-ls
   /reference/minio-mc/mc-ilm-tier-rm
   /reference/minio-mc/mc-ilm-tier-update
