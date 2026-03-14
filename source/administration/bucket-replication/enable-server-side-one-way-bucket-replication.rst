.. _minio-bucket-replication-serverside-oneway:

=============================================
启用服务端单向存储桶复制
=============================================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2


本页面中的过程会创建一条新的存储桶复制规则，用于将对象从一个 MinIO 存储桶单向复制到另一个 MinIO 存储桶。
这些存储桶既可以位于同一个 MinIO 部署中，也可以位于不同的 MinIO 部署中。

.. image:: /images/replication/active-passive-oneway-replication.svg
   :width: 800px
   :alt: 主动-被动复制会将数据从源 MinIO 部署同步到远程 MinIO 部署。
   :align: center


- 如需在 MinIO 存储桶之间配置双向“active-active”复制，请参见 :ref:`minio-bucket-replication-serverside-twoway`。
- 如需在 MinIO 部署之间配置多站点“active-active”复制，请参见 :ref:`minio-bucket-replication-serverside-multi`

.. note::

   如需在任意兼容 S3 的服务之间配置复制（不一定是 MinIO），请使用 :mc:`mc mirror`。


要求
------------


复制要求所有参与的集群满足 :ref:`以下要求 <minio-bucket-replication-requirements>`。
本过程假定你已经审阅并验证了这些要求。

如需更多详细信息，请参见 :ref:`存储桶复制要求 <minio-bucket-replication-requirements>` 页面。


注意事项
--------------

点击展开以下任一项：

.. dropdown:: 现有对象的复制
   :icon: fold-down

   MinIO 支持自动复制存储桶中的现有对象。

   MinIO 要求使用 :mc-cmd:`mc replicate add --replicate` 或 :mc-cmd:`mc replicate update --replicate` 显式启用现有对象复制，并包含 ``existing-objects`` 复制功能标志。
   本过程包含启用现有对象复制所需的标志。

.. dropdown:: 删除操作的复制
   :icon: fold-down

   MinIO 支持将 S3 ``DELETE`` 操作复制到目标存储桶。
   具体来说，MinIO 可以复制版本控制的 :s3-docs:`Delete Markers <versioning-workflows.html>`，以及删除特定版本对象的操作：

   - 对于对象的删除操作，MinIO 复制也会在目标存储桶上创建删除标记。

   - 对于对象某个版本的删除操作，MinIO 复制也会在目标存储桶上删除这些版本。

   MinIO 要求使用 :mc-cmd:`mc replicate add --replicate` 或 :mc-cmd:`mc replicate update --replicate` 显式启用删除操作复制。
   本过程包含启用删除操作和删除标记复制所需的标志。

   MinIO *不会* 复制因应用 :ref:`生命周期管理过期规则 <minio-lifecycle-management-expiration>` 而产生的删除操作。

   更完整的文档请参见 :ref:`minio-replication-behavior-delete` 和 :ref:`minio-object-delete`。

.. dropdown:: 多站点复制
   :icon: fold-down

   MinIO 支持为每个存储桶或存储桶前缀配置多个远程目标。
   例如，你可以将一个存储桶配置为把数据复制到两个或更多远程 MinIO 部署，其中一个部署是 1:1 副本（复制包括删除在内的所有操作），另一个部署则是完整的历史记录（仅复制非破坏性的写入操作）。

   本过程说明了到单个远程 MinIO 部署的单向复制。
   你可以重复本教程，将单个存储桶复制到多个远程目标。

过程
---------

- :ref:`使用命令行配置单向存储桶复制 <minio-bucket-replication-one-way-minio-cli-procedure>`
   - :ref:`创建复制远程目标 <minio-bucket-replication-one-way-minio-cli-create-remote-targets>`
   - :ref:`创建新的存储桶复制规则 <minio-bucket-replication-one-way-minio-cli-create-replication-rules>`
   - :ref:`验证复制配置 <minio-bucket-replication-one-way-minio-cli-verify-replication-config>` 


.. _minio-bucket-replication-one-way-minio-cli-procedure:

使用命令行 ``mc`` 配置单向存储桶复制
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

本过程使用 :ref:`别名 <alias>` ``SOURCE`` 和 ``REMOTE`` 来引用每个配置了复制的 MinIO 部署。
请将这些值替换为你的目标 MinIO 部署对应的别名。

本过程假定每个别名都对应一个具有 :ref:`必要复制权限 <minio-bucket-replication-serverside-oneway-permissions>` 的用户。

.. versionchanged:: RELEASE.2022-12-24T15-21-38Z

   :mc:`mc replicate add` 会自动创建所需的复制目标，因此不再需要使用已弃用的 ``mc admin remote bucket add`` 命令。
   本过程仅说明该版本起的操作流程。

.. _minio-bucket-replication-one-way-minio-cli-create-remote-targets:

.. _minio-bucket-replication-one-way-minio-cli-create-replication-rules:

1) 创建新的存储桶复制规则
+++++++++++++++++++++++++++++++++++++++

.. include:: /includes/common/bucket-replication.rst
   :start-after: start-create-bucket-replication-rule-cli-desc
   :end-before: end-create-bucket-replication-rule-cli-desc

.. _minio-bucket-replication-one-way-minio-cli-verify-replication-config:

2) 验证复制配置
+++++++++++++++++++++++++++++++++++++++++

.. include:: /includes/common/bucket-replication.rst
   :start-after: start-validate-bucket-replication-cli-desc
   :end-before: end-validate-bucket-replication-cli-desc

.. seealso::

   - 使用 :mc:`mc replicate update` 命令修改现有复制规则。

   - 使用 :mc:`mc replicate update` 命令并配合 :mc-cmd:`--state "disable" <mc replicate update --state>` 标志禁用现有复制规则。

   - 使用 :mc:`mc replicate rm` 命令删除现有复制规则。
