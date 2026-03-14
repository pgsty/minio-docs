===============
``mc ilm rule``
===============

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc ilm rule

.. versionchanged:: RELEASE.2022-12-24T15-21-38Z

   以下命令已移至 :mc-cmd:`mc ilm rule` 下的子命令：

   - :mc-cmd:`mc ilm add`
   - :mc-cmd:`mc ilm edit`
   - :mc-cmd:`mc ilm export`
   - :mc-cmd:`mc ilm import`
   - :mc-cmd:`mc ilm ls`
   - :mc-cmd:`mc ilm rm`


描述
----

.. start-mc-ilm-rule-desc

:mc:`mc ilm rule` 命令及其子命令用于配置 MinIO 生命周期管理中对象在各存储层之间转换所使用的规则。

.. end-mc-ilm-rule-desc

在使用此命令创建规则前，请先使用 :mc-cmd:`mc ilm tier` 及其子命令创建对象将要迁移到的其他对象存储位置对应的一个或多个存储层。

有关更多信息，请参阅 :ref:`lifecycle management <minio-lifecycle-management>` 概述。


子命令
------

:mc-cmd:`mc ilm rule` 包含以下子命令：

.. list-table::
   :header-rows: 1
   :widths: 30 70
   :width: 100%

   * - 子命令
     - 描述

   * - :mc:`~mc ilm rule add`
     - .. include:: /reference/minio-mc/mc-ilm-rule-add.rst
          :start-after: start-mc-ilm-rule-add-desc
          :end-before: end-mc-ilm-rule-add-desc

   * - :mc:`~mc ilm rule edit`
     - .. include:: /reference/minio-mc/mc-ilm-rule-edit.rst
          :start-after: start-mc-ilm-rule-edit-desc
          :end-before: end-mc-ilm-rule-edit-desc

   * - :mc:`~mc ilm rule export`
     - .. include:: /reference/minio-mc/mc-ilm-rule-export.rst
          :start-after: start-mc-ilm-rule-export-desc
          :end-before: end-mc-ilm-rule-export-desc

   * - :mc:`~mc ilm rule import`
     - .. include:: /reference/minio-mc/mc-ilm-rule-import.rst
          :start-after: start-mc-ilm-rule-import-desc
          :end-before: end-mc-ilm-rule-import-desc

   * - :mc:`~mc ilm rule ls`
     - .. include:: /reference/minio-mc/mc-ilm-rule-ls.rst
          :start-after: start-mc-ilm-rule-ls-desc
          :end-before: end-mc-ilm-rule-ls-desc

   * - :mc:`~mc ilm rule rm`
     - .. include:: /reference/minio-mc/mc-ilm-rule-rm.rst
          :start-after: start-mc-ilm-rule-rm-desc
          :end-before: end-mc-ilm-rule-rm-desc

.. _minio-mc-ilm-rule-permissions:

权限
----

MinIO 要求具备以下权限，且权限范围应限定为创建生命周期管理规则的一个或多个存储桶。

- :policy-action:`s3:PutLifecycleConfiguration`
- :policy-action:`s3:GetLifecycleConfiguration`

例如，以下策略提供了在集群任意存储桶上配置对象转换生命周期管理规则的权限：

.. literalinclude:: /extra/examples/LifecycleManagementAdmin.json
   :language: json
   :class: copyable

转换权限
~~~~~~~~

对象转换生命周期管理规则在远端存储层上需要额外权限。具体而言，
MinIO 要求远端存储层凭证提供读取、写入、列举和删除权限。

例如，如果远端存储层使用基于 AWS IAM 策略的访问控制，
则以下策略提供了对象迁入和迁出远端存储层所需的必要权限：

.. literalinclude:: /extra/examples/LifecycleManagementUser.json
   :language: json
   :class: copyable

请根据 MinIO 分层写入对象的目标存储桶修改 ``Resource``。

有关如何配置用户和权限以支持 MinIO 分层的完整信息，请参阅受支持分层目标的文档：

- :aws-docs:`Amazon S3 Permissions <service-authorization/latest/reference/list_amazons3.html#amazons3-actions-as-permissions>`
- `Google Cloud Storage Access Control <https://cloud.google.com/storage/docs/access-control>`__
- `Authorizing access to data in Azure storage <https://docs.microsoft.com/en-us/azure/storage/common/storage-auth?toc=/azure/storage/blobs/toc.json>`__

.. toctree::
   :titlesonly:
   :hidden:
   
   /reference/minio-mc/mc-ilm-rule-add
   /reference/minio-mc/mc-ilm-rule-edit
   /reference/minio-mc/mc-ilm-rule-export
   /reference/minio-mc/mc-ilm-rule-import
   /reference/minio-mc/mc-ilm-rule-ls
   /reference/minio-mc/mc-ilm-rule-rm
