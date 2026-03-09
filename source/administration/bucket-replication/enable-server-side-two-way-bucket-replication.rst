.. _minio-bucket-replication-serverside-twoway:

==========================
启用双向服务端存储桶复制
==========================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

本页中的过程会创建新的存储桶复制规则，用于在 MinIO 存储桶之间对对象执行双向“主动-主动”同步。

.. image:: /images/replication/active-active-twoway-replication.svg
   :width: 800px
   :alt: 主动-主动复制在两个远程集群之间同步数据。
   :align: center

- 如需在任意兼容 S3 的服务之间配置复制，请使用 :mc:`mc mirror`。

- 如需在 MinIO 集群之间配置单向“主动-被动”复制，请参见 :ref:`minio-bucket-replication-serverside-oneway`。
  
- 如需在 MinIO 集群之间配置多站点“主动-主动”复制，请参见 :ref:`minio-bucket-replication-serverside-multi`。

本教程介绍如何在两个 MinIO 集群之间配置主动-主动复制。有关在三个或更多 MinIO 集群之间进行多站点复制的教程，请参见 :ref:`minio-bucket-replication-serverside-multi`。


.. _minio-bucket-replication-serverside-twoway-requirements:

要求
----

您必须满足 :ref:`存储桶复制要求 <minio-bucket-replication-requirements>` 中描述的所有存储桶复制基础要求。

此外，要设置主动-主动存储桶复制，您还必须满足以下附加要求：

.. _minio-bucket-replication-serverside-twoway-permissions:

访问两个集群
~~~~~~~~~~~~

要设置主动-主动存储桶复制，您必须能够通过网络访问两个部署，并拥有具有所需权限的登录凭证。

您可以安装 :mc:`mc` 并通过命令行访问这些部署。
使用 :mc:`mc alias set` 命令为两个 MinIO 部署创建别名。

创建别名时，需要为部署中的某个用户指定 access key。
该用户**必须**具有在该部署上创建和管理用户及策略的权限。

具体而言，请确保该用户*至少*具有以下权限：

- :policy-action:`admin:CreateUser`
- :policy-action:`admin:ListUsers`
- :policy-action:`admin:GetUser`
- :policy-action:`admin:CreatePolicy`
- :policy-action:`admin:GetPolicy`
- :policy-action:`admin:AttachUserOrGroupPolicy`


注意事项
--------

.. dropdown:: 使用一致的复制设置
   :icon: fold-down

   MinIO 支持自定义复制配置，以启用或禁用以下复制行为：

   - :ref:`删除操作 <minio-object-delete>` 的复制
   - 删除标记的复制
   - 现有对象的复制
   - 仅元数据变更的复制

   为存储桶配置复制规则时，请确保参与主动-主动复制的两个 MinIO 部署使用*相同*的复制行为，以确保对象同步一致且可预测。

.. dropdown:: 现有对象的复制
   :icon: fold-down

   MinIO 支持自动复制存储桶中的现有对象。

   MinIO 要求使用 :mc-cmd:`mc replicate add --replicate` 或 :mc-cmd:`mc replicate update --replicate` 显式启用现有对象复制，并包含 ``existing-objects`` 复制功能标志。
   此过程包含启用现有对象复制所需的标志。

.. dropdown:: 删除操作的复制
   :icon: fold-down

   MinIO 支持将删除操作复制到目标存储桶。
   具体而言，MinIO 可以复制版本控制中的 :s3-docs:`Delete Markers <versioning-workflows.html>` 以及特定已版本化对象的删除：

   - 对于对象上的删除操作，MinIO 复制也会在目标存储桶上创建删除标记。

   - 对于对象某个版本的删除操作，MinIO 复制也会删除目标存储桶上的这些版本。

   MinIO 要求使用 :mc-cmd:`mc replicate add --replicate` 或 :mc-cmd:`mc replicate update --replicate` 显式启用删除操作复制。
   此过程包含启用删除操作和删除标记复制所需的标志。

   MinIO *不会* 复制因应用 :ref:`lifecycle management expiration rules <minio-lifecycle-management-expiration>` 而产生的删除操作。
   请在源存储桶和目标存储桶上配置匹配的过期规则，以确保对象过期行为一致。

   有关更完整的文档，请参见 :ref:`minio-replication-behavior-delete` 和 :ref:`minio-object-delete`。

.. dropdown:: 多站点复制
   :icon: fold-down

   MinIO 支持为每个存储桶或存储桶前缀配置多个远程目标。
   这使得可以在 MinIO 部署之间配置多站点主动-主动复制。

   本过程介绍 *两个* MinIO 站点之间的主动-主动复制。
   您可以针对复制网格中的每一“对”MinIO 部署重复此过程。有关专门教程，请参见 :ref:`minio-bucket-replication-serverside-multi`。

操作步骤
--------

- :ref:`使用命令行配置双向存储桶复制 <minio-bucket-replication-two-way-minio-cli-procedure>`
   - :ref:`创建复制远程目标 <minio-bucket-replication-two-way-minio-cli-create-remote-targets>`
   - :ref:`在每个部署上创建新的存储桶复制规则 <minio-bucket-replication-two-way-minio-cli-create-replication-rules>`
   - :ref:`验证复制配置 <minio-bucket-replication-two-way-minio-cli-verify-replication-config>` 

.. _minio-bucket-replication-two-way-minio-cli-procedure:

使用命令行 ``mc`` 配置双向存储桶复制
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

此过程会在两个 MinIO 部署之间创建双向、主动-主动复制。

此过程假定您已使用具有 :ref:`所需复制权限 <minio-bucket-replication-serverside-twoway-permissions>` 的用户为每个部署定义了别名。

.. versionchanged:: RELEASE.2022-12-24T15-21-38Z

   :mc:`mc replicate add` 会自动创建所需的复制目标，因此不再需要使用已弃用的 ``mc admin remote bucket add`` 命令。
   本文档仅说明自该版本起的过程。

.. _minio-bucket-replication-two-way-minio-cli-create-remote-targets:

.. _minio-bucket-replication-two-way-minio-cli-create-replication-rules:

1) 在每个部署上创建新的存储桶复制规则
+++++++++++++++++++++++++++++++++++++++

.. include:: /includes/common/bucket-replication.rst
   :start-after: start-create-bucket-replication-rule-cli-desc
   :end-before: end-create-bucket-replication-rule-cli-desc

在另一个 MinIO 部署上重复此步骤。
将 ``ALIAS`` 和 ``--remote-bucket`` 的值修改为与第一个部署对应。

完成此步骤后，您应已配置两条复制规则：每个部署上一条，并指向另一个部署上的存储桶。
使用 :mc:`mc replicate ls` 命令验证已创建的复制规则。

.. _minio-bucket-replication-two-way-minio-cli-verify-replication-config:

2) 验证复制配置
+++++++++++++++

.. include:: /includes/common/bucket-replication.rst
   :start-after: start-validate-bucket-replication-cli-desc
   :end-before: end-validate-bucket-replication-cli-desc

通过将另一个对象复制到第二个部署，并验证该对象会复制到第一个部署，来重复执行此测试。

当两个对象都存在于两个部署上时，您就已成功在 MinIO 存储桶之间设置了双向、主动-主动复制。

.. seealso::

   - 使用 :mc:`mc replicate update` 命令修改现有复制规则。

   - 使用 :mc:`mc replicate update` 命令并配合 :mc-cmd:`--state "disable" <mc replicate update --state>` 标志禁用现有复制规则。

   - 使用 :mc:`mc replicate rm` 命令移除现有复制规则。
