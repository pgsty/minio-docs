.. _minio-bucket-replication-serverside-multi:

====================================
启用多站点服务端存储桶复制
====================================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2


本页中的过程用于在多个 MinIO 部署之间配置自动化的服务端存储桶复制。多站点 Active-Active 复制基于 :ref:`minio-bucket-replication-serverside-twoway` 过程，并增加了额外注意事项，以确保所有站点上的复制行为可预测。

.. image:: /images/replication/active-active-multi-replication.svg
   :width: 600px
   :alt: Active-Active 复制可在多个远程部署之间同步数据。
   :align: center

- 如需在任意 S3 兼容服务之间配置复制，请使用 :mc:`mc mirror`。

- 如需在两个 MinIO 部署之间配置单向 “active-active” 复制，请参阅 :ref:`minio-bucket-replication-serverside-twoway`。

- 如需在 MinIO 部署之间配置单向 “active-passive” 复制，请参阅 :ref:`minio-bucket-replication-serverside-oneway`。

多站点 Active-Active 复制配置可以跨越多个机架、数据中心或地理位置。多站点配置的部署与维护复杂度通常会随着站点数量和每个站点规模的增加而提高。计划实施多站点复制的企业应考虑借助 `MinIO SUBNET <https://min.io/pricing?ref=docs>`__ 支持，以获取应对此类用例所需的专业知识、规划能力和工程资源。

.. seealso::

   - 使用 :mc:`mc replicate update` 命令修改现有复制规则。

   - 使用带有 :mc-cmd:`--state "disable" <mc replicate update --state>` 标志的 :mc:`mc replicate update` 命令禁用现有复制规则。

   - 使用 :mc:`mc replicate rm` 命令删除现有复制规则。

.. _minio-bucket-replication-serverside-multi-requirements:

要求
----

你必须满足 :ref:`Bucket Replication Requirements <minio-bucket-replication-requirements>` 中描述的所有存储桶复制基础要求。

此外，要创建多站点存储桶复制配置，你还必须满足以下额外要求：

访问所有集群
~~~~~~~~~~~~

要设置多站点 Active-Active 存储桶复制，你必须具备访问所有部署的网络连通性，以及具有正确权限的登录凭证。

你可以通过安装 :mc:`mc` 并使用命令行访问这些部署。
使用 :mc:`mc alias set` 命令为每个 MinIO 部署创建别名。

创建别名时，需要指定该部署上某个用户的 access key。
该用户**必须**具有在该部署上创建和管理用户及策略的权限。

具体来说，请确保该用户*至少*具有以下权限：

- :policy-action:`admin:CreateUser`
- :policy-action:`admin:ListUsers`
- :policy-action:`admin:GetUser`
- :policy-action:`admin:CreatePolicy`
- :policy-action:`admin:GetPolicy`
- :policy-action:`admin:AttachUserOrGroupPolicy`


注意事项
--------

点击展开以下任意条目：

.. dropdown:: 使用一致的复制设置
   :icon: fold-down

   MinIO 支持自定义复制配置，以启用或禁用以下复制行为：

   - 复制 :ref:`delete operations <minio-object-delete>`
   - 复制删除标记
   - 复制现有对象
   - 复制仅元数据变更

   为存储桶配置复制规则时，请确保参与多站点复制的所有 MinIO 部署使用*相同*的复制行为，以保证对象同步的一致性和可预测性。

.. dropdown:: 现有对象复制
   :icon: fold-down

   MinIO 支持自动复制存储桶中的现有对象。

   MinIO 要求使用 :mc-cmd:`mc replicate add --replicate` 或 :mc-cmd:`mc replicate update --replicate` 显式启用现有对象复制，并包含 ``existing-objects`` 复制功能标志。
   本过程包含用于启用现有对象复制的必需标志。

.. dropdown:: 删除操作复制
   :icon: fold-down

   MinIO 支持将 :ref:`delete operations <minio-object-delete>` 复制到目标存储桶。
   具体来说，MinIO 可以复制版本控制中的 :s3-docs:`Delete Markers <versioning-workflows.html>`，以及删除指定版本对象的操作：

   - 对对象执行删除操作时，MinIO 复制也会在目标存储桶上创建删除标记。

   - 对对象的某个版本执行删除操作时，MinIO 复制也会在目标存储桶上删除这些版本。

   MinIO 要求使用 :mc-cmd:`mc replicate add --replicate` 或 :mc-cmd:`mc replicate update --replicate` 显式启用删除操作复制。
   本过程包含用于启用删除操作和删除标记复制的必需标志。

   MinIO *不会* 复制因应用 :ref:`lifecycle management expiration rules <minio-lifecycle-management-expiration>` 而产生的删除操作。
   请在所有复制站点上为该存储桶配置一致的过期规则，以确保对象过期策略得到一致应用。

过程
----

对于参与多站点复制配置的每个 MinIO 部署，都需要重复执行本过程中的步骤。根据部署数量的不同，该过程在实施时可能需要投入大量时间并格外谨慎。MinIO 建议在尝试执行文档中的步骤之前，先完整阅读本过程。

- 使用命令行配置多站点存储桶复制
   
   - :ref:`创建新的存储桶复制规则 <minio-bucket-replication-multi-site-minio-cli-create-replication-rules>`
   - :ref:`验证复制配置 <minio-bucket-replication-multi-site-minio-cli-verify-replication-config>` 


使用命令行 ``mc`` 配置多站点存储桶复制
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

本过程使用占位符 ``ALIAS`` 来引用每个被配置为复制端点的 MinIO 部署的 :ref:`alias <alias>`。
请将这些值替换为各个 MinIO 部署对应的实际别名。

本过程假设每个别名都对应一个具备 :ref:`necessary replication permissions <minio-bucket-replication-requirements>` 的用户。

.. versionchanged:: RELEASE.2022-12-24T15-21-38Z

   :mc:`mc replicate add` 会自动创建所需的复制目标，因此不再需要使用已弃用的 ``mc admin remote bucket add`` 命令。
   本过程仅记录该版本及之后的操作方式。

.. _minio-bucket-replication-multi-site-minio-cli-create-replication-rules:

1) 创建新的存储桶复制规则
++++++++++++++++++++++++++++++++++++++

.. include:: /includes/common/bucket-replication.rst
   :start-after: start-create-bucket-replication-rule-cli-desc
   :end-before: end-create-bucket-replication-rule-cli-desc

对于参与多站点复制配置的每个远程 MinIO 部署，都要重复执行这些命令。
例如，一个由 MinIO 部署 ``minio1``、``minio2`` 和 ``minio3`` 组成的多站点复制配置，需要在每个部署上针对每个远程端重复执行此步骤。
         
具体来说，在这种场景下，需要在每个部署上执行两次此步骤：

- 在 ``minio1`` 部署上，为 ``minio2`` 创建一条规则，再为 ``minio3`` 单独创建另一条规则。

- 在 ``minio2`` 部署上，为 ``minio1`` 创建一条规则，再为 ``minio3`` 单独创建另一条规则。

- 在 ``minio3`` 部署上，为 ``minio1`` 创建一条规则，再为 ``minio2`` 单独创建另一条规则。

.. _minio-bucket-replication-multi-site-minio-cli-verify-replication-config:

2) 验证复制配置
+++++++++++++++

.. include:: /includes/common/bucket-replication.rst
   :start-after: start-validate-bucket-replication-cli-desc
   :end-before: end-validate-bucket-replication-cli-desc

在每个部署上重复执行此测试：复制一个新的唯一文件，并检查该文件是否已复制到其他每个部署。

你也可以使用 :mc:`mc stat` 检查该文件，以查看对象当前的 :ref:`replication stage <minio-replication-process>`。
