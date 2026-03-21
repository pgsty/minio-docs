.. _minio-policy:

=================
访问管理
=================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 1

概述
----

MinIO 使用 基于策略的访问控制 (PBAC) 来定义已认证用户可访问的授权操作和资源。
每个策略描述一条或多条 :ref:`actions <minio-policy-actions>` 与 :ref:`conditions <minio-policy-conditions>`，用于说明某个 :ref:`user <minio-users>` 或 :ref:`group <minio-groups>` 中用户的权限。

MinIO PBAC 在设计上兼容 AWS IAM 策略语法、结构和行为。
MinIO 文档会尽力覆盖 IAM 特定行为和功能。
若需要更完整的 AWS IAM 特定主题文档，请参考 :iam-docs:`IAM documentation <>`。

:mc:`mc admin policy` 命令支持在 MinIO 部署上创建和管理策略。
用法示例请参见命令参考。

基于标签的策略条件
------------------

.. versionchanged:: RELEASE.2022-10-02T19-29-29Z

   策略可以使用条件，将用户访问限制为仅能访问带有 :ref:`特定标签 <minio-object-tagging>` 的对象。

   对于 :ref:`选定操作 <minio-selected-conditional-actions>` 的策略，MinIO 支持 :s3-docs:`基于标签的条件 <tagging-and-policies.html>`。
   在策略的 ``Condition`` 语句中使用 ``s3:ExistingObjectTag/<key>``。

.. _minio-policy-built-in:

内置策略
--------

MinIO 提供以下内置策略，可分配给
:ref:`users <minio-users>` 或 :ref:`groups <minio-groups>`：

.. userpolicy:: consoleAdmin

   授予对 MinIO 部署上所有资源执行全部 S3 和管理 API 操作的完整访问权限。
   等价于以下 action 集合：

   - :policy-action:`s3:*`
   - :policy-action:`admin:*`

.. userpolicy:: readonly

   授予对 MinIO 部署上任意对象的只读权限。
   ``GET`` 操作 *必须* 作用于某个具体对象，且不要求具备任何列举权限。
   等价于以下 action 集合：

   - :policy-action:`s3:GetBucketLocation`
   - :policy-action:`s3:GetObject`

   例如，该策略专门支持对特定路径下对象执行 ``GET`` 操作（例如 ``GET play/mybucket/object.file``），例如：

   - :mc:`mc cp`
   - :mc:`mc stat`
   - :mc:`mc head`
   - :mc:`mc cat`

   有意排除列举权限，因为典型使用场景并不希望“只读”角色对对象存储资源具有完整可发现性
   （列出所有存储桶和对象）。

.. userpolicy:: readwrite

   授予对 MinIO 服务器上所有存储桶和对象的读写权限。
   等价于 :policy-action:`s3:*`。

.. userpolicy:: diagnostics

   授予在 MinIO 部署上执行诊断操作的权限。
   具体包括以下 action：

   - :policy-action:`admin:ServerTrace`
   - :policy-action:`admin:Profiling`
   - :policy-action:`admin:ConsoleLog`
   - :policy-action:`admin:ServerInfo`
   - :policy-action:`admin:TopLocksInfo`
   - :policy-action:`admin:OBDInfo`
   - :policy-action:`admin:BandwidthMonitor`
   - :policy-action:`admin:Prometheus`

.. userpolicy:: writeonly

   授予对 MinIO 部署中任意命名空间（存储桶及对象路径）的只写权限。
   ``PUT`` 操作 *必须* 作用于某个具体对象位置，且不要求具备任何列举权限。
   等价于 :policy-action:`s3:PutObject` action。

使用 :mc:`mc admin policy attach` 将策略关联到 MinIO 部署上的用户或组。

例如，考虑下表中的用户。每个用户都被分配了一个
:ref:`内置策略 <minio-policy-built-in>` 或某个受支持的
:ref:`action <minio-policy-actions>`。该表描述了客户端以对应用户身份完成认证后可执行的一部分操作：

.. list-table::
   :header-rows: 1
   :widths: 20 40 40
   :width: 100%

   * - 用户
     - 策略
     - 操作

   * - ``Operations``
     - | ``finance`` 存储桶上的 :userpolicy:`readwrite`
       | ``audit`` 存储桶上的 :userpolicy:`readonly`

     - | 对 ``finance`` 存储桶执行 ``PUT`` 和 ``GET``。
       | 对 ``audit`` 存储桶执行 ``GET``

   * - ``Auditing``
     - | ``audit`` 存储桶上的 :userpolicy:`readonly`
     - 对 ``audit`` 存储桶执行 ``GET``

   * - ``Admin``
     - :policy-action:`admin:*`
     - 所有 :mc:`mc admin` 命令。

每个用户只能访问内置角色 *显式* 授予的那些资源和操作。
默认情况下，MinIO 会拒绝访问任何其他资源或 action。

.. admonition:: ``Deny`` overrides ``Allow``
   :class: note

   MinIO 遵循 IAM 策略求值规则，即在同一操作/资源上，``Deny`` 规则会覆盖
   ``Allow`` 规则。例如，如果某个用户被显式分配的策略对某个操作/资源包含
   ``Allow`` 规则，而其所属某个组被分配的策略对同一操作/资源包含 ``Deny`` 规则，
   则 MinIO 只会应用 ``Deny`` 规则。

   有关 IAM 策略求值逻辑的更多信息，请参见 IAM 文档中的
   :iam-docs:`Determining Whether a Request is Allowed or Denied Within an Account
   <reference_policies_evaluation-logic.html#policy-eval-denyallow>`。

.. _minio-policy-document:

策略文档结构
------------

MinIO 策略文档使用与
:aws-docs:`AWS IAM Policy <IAM/latest/UserGuide/access.html>` 文档相同的模式。

以下示例文档为在 MinIO 部署中创建自定义策略提供了模板。
有关 IAM 策略元素的更完整文档，请参见 :aws-docs:`IAM JSON Policy Elements Reference <IAM/latest/UserGuide/reference_policies_elements.html>`。

任意单个策略文档的最大大小为 20KiB。
可附加到用户或组的策略文档数量没有限制。

.. code-block:: javascript
   :class: copyable

   {
      "Version" : "2012-10-17",
      "Statement" : [
         {
            "Effect" : "Allow",
            "Action" : [ "s3:<ActionName>", ... ],
            "Resource" : "arn:aws:s3:::*",
            "Condition" : { ... }
         },
         {
            "Effect" : "Deny",
            "Action" : [ "s3:<ActionName>", ... ],
            "Resource" : "arn:aws:s3:::*",
            "Condition" : { ... }
         }
      ]
   }

- 对于 ``Statement.Action`` 数组，指定一个或多个 :ref:`受支持的 S3 API 操作 <minio-policy-actions>`。

- 对于 ``Statement.Resource`` 键，指定要对策略进行限制的存储桶或存储桶前缀。
  可以按照 :s3-docs:`S3 Resource Spec <s3-arn-format.html>` 使用 ``*`` 和 ``?`` 通配符。

  ``*`` 通配符可能会基于 :ref:`模式匹配 <minio-wildcard-matching>`，导致策略被意外应用到多个存储桶或前缀。
  例如，``arn:aws:s3:::data*`` 会匹配存储桶 ``data``、``data_private`` 和 ``data_internal``。
  如果资源键仅指定 ``*``，则该策略会应用到部署上的所有存储桶和前缀。

- 对于 ``Statement.Condition`` 键，可以指定一个或多个 :ref:`受支持的 Conditions <minio-policy-conditions>`。

.. _minio-policy-actions:

受支持的 S3 策略 Action
-----------------------

MinIO 策略文档支持 IAM :iam-docs:`S3 Action keys <list_amazons3.html#amazons3-actions-as-permissions>` 的一个子集。
本节还包括特定 action 在通用受支持键之外额外支持的任何 :ref:`condition keys <minio-policy-conditions>`。

以下 action 用于控制常见 S3 操作的访问。
其余小节记录更高级的 S3 操作所对应的 action：

.. policy-action:: s3:*

   选择器，用于匹配 *所有* MinIO S3 操作。
   将该 action 应用于某个资源后，用户即可对该资源执行 *任意* S3 操作。

.. policy-action:: s3:CreateBucket

   控制对 :s3-api:`CreateBucket <API_CreateBucket.html>` S3 API
   操作的访问。

.. policy-action:: s3:DeleteBucket

   控制对 :s3-api:`DeleteBucket <API_DeleteBucket.html>` S3 API
   操作的访问。

.. policy-action:: s3:ForceDeleteBucket

   控制对带有 ``x-minio-force-delete`` 标志的
   :s3-api:`DeleteBucket <API_DeleteBucket.html>` S3 API 操作的访问。
   删除非空存储桶时需要此权限。

.. policy-action:: s3:GetBucketLocation

   控制对 :s3-api:`GetBucketLocation <API_GetBucketLocation.html>` S3 API 操作的访问。

.. policy-action:: s3:ListAllMyBuckets

   控制对 :s3-api:`ListBuckets <API_ListBuckets.html>` S3 API 操作的访问。

.. policy-action:: s3:DeleteObject

   控制对 :s3-api:`DeleteObject <API_DeleteObject.html>` S3 API 操作的访问。

.. policy-action:: s3:GetObject

   控制对 :s3-api:`GetObject <API_GetObject.html>` S3 API 操作的访问。

   支持以下附加 :ref:`condition keys <minio-policy-conditions>`：

   .. code-block:: shell

      s3:x-amz-server-side-encryption
      s3:x-amz-server-side-encryption-customer-algorithm
      s3:ExistingObjectTag/<key>
      s3:versionid

.. policy-action:: s3:GetObjectAttributes

   控制对 :s3-api:`GetObjectAttributes <API_GetObjectAttributes.html>` S3 API 操作的访问。

.. policy-action:: s3:GetObjectVersionAttributes

   控制对带版本对象执行 :s3-api:`GetObjectAttributes <API_GetObjectAttributes.html>` S3 API 操作的访问。

.. policy-action:: s3:RestoreObject

   控制对 :s3-api:`RestoreObject <API_RestoreObject.html>` S3 API 操作的访问。

.. policy-action:: s3:ListBucket

   控制对 :s3-api:`ListObjectsV2 <API_ListObjectsV2.html>` S3 API 操作的访问。

   支持以下附加 :ref:`condition keys <minio-policy-conditions>`：

   .. code-block:: shell

      s3:prefix
      s3:delimiter
      s3:max-keys

.. policy-action:: s3:PutObject

   控制对 :s3-api:`PutObject <API_PutObject.html>` S3 API 操作的访问。

   支持以下附加 :ref:`condition keys <minio-policy-conditions>`：

   .. code-block:: shell

      s3:x-amz-copy-source
      s3:x-amz-server-side-encryption
      s3:x-amz-server-side-encryption-customer-algorithm
      s3:x-amz-metadata-directive
      s3:x-amz-storage-class
      s3:versionid
      s3:object-lock-retain-until-date
      s3:object-lock-mode
      s3:object-lock-legal-hold
      s3:RequestObjectTagKeys
      s3:RequestObjectTag/<key>

.. policy-action:: s3:PutObjectTagging

   控制对 :s3-api:`PutObjectTagging <API_PutObjectTagging.html>` S3 API 操作的访问。

   支持以下附加 :ref:`condition keys <minio-policy-conditions>`：

   .. code-block:: shell

      s3:versionid
      s3:ExistingObjectTag/<key>
      s3:RequestObjectTagKeys
      s3:RequestObjectTag/<key>

.. policy-action:: s3:GetObjectTagging

   控制对 :s3-api:`GetObjectTagging <API_GetObjectTagging.html>` S3 API 操作的访问。

   支持以下附加 :ref:`condition keys <minio-policy-conditions>`：

   .. code-block:: shell

     s3:versionid
     s3:ExistingObjectTag/<key>

.. policy-action:: s3:DeleteObjectTagging

   控制对 :s3-api:`DeleteObjectTagging <API_DeleteObjectTagging.html>` S3 API 操作的访问。

   支持以下附加 :ref:`condition keys <minio-policy-conditions>`：

   .. code-block:: shell

     s3:versionid
     s3:ExistingObjectTag/<key>

存储桶配置
~~~~~~~~~~

.. policy-action:: s3:GetBucketPolicy

   控制对 :s3-api:`GetBucketPolicy <API_GetBucketPolicy.html>` S3 API 操作的访问。

.. policy-action:: s3:PutBucketPolicy

   控制对 :s3-api:`PutBucketPolicy <API_PutBucketPolicy.html>`
   S3 API 操作的访问。

.. policy-action:: s3:DeleteBucketPolicy

   控制对 :s3-api:`DeleteBucketPolicy <API_DeleteBucketPolicy.html>` S3 API 操作的访问。

.. policy-action:: s3:GetBucketTagging

   控制对 :s3-api:`GetBucketTagging <API_GetBucketTagging.html>` S3 API 操作的访问。

.. policy-action:: s3:PutBucketTagging

   控制对 :s3-api:`PutBucketTagging <API_PutBucketTagging.html>` S3 API 操作的访问。

   支持以下附加 :ref:`condition keys <minio-policy-conditions>`：

   .. code-block:: shell

      s3:RequestObjectTagKeys
      s3:RequestObjectTag/<key>

.. policy-action:: s3:GetBucketPolicyStatus

   控制对 :s3-api:`GetBucketPolicyStatus <API_GetBucketPolicyStatus.html>` S3 API 操作的访问。

分段上传
~~~~~~~~

.. policy-action:: s3:AbortMultipartUpload

   控制对 :s3-api:`AbortMultipartUpload <API_AbortMultipartUpload.html>` S3 API 操作的访问。

.. policy-action:: s3:ListMultipartUploadParts

   控制对 :s3-api:`ListParts <API_ListParts.html>` S3 API
   操作的访问。

.. policy-action:: s3:ListBucketMultipartUploads

   控制对 :s3-api:`ListMultipartUploads <API_ListMultipartUploads.html>` S3 API 操作的访问。

版本控制与保留
~~~~~~~~~~~~~~

.. policy-action:: s3:PutBucketVersioning

   控制对 :s3-api:`PutBucketVersioning <API_PutBucketVersioning.html>` S3 API 操作的访问。

.. policy-action:: s3:GetBucketVersioning

   控制对 :s3-api:`GetBucketVersioning <API_GetBucketVersioning.html>` S3 API 操作的访问。

.. policy-action:: s3:DeleteObjectVersion

   控制对 :s3-api:`DeleteObjectVersion <API_DeleteObjectVersion.html>` S3 API 操作的访问。

   支持以下附加 :ref:`condition keys <minio-policy-conditions>`：

   .. code-block:: shell

      s3:versionid
      s3:ExistingObjectTag/<key>


.. policy-action:: s3:ListBucketVersions

   控制对 :s3-api:`ListBucketVersions <API_ListBucketVersions.html>` S3 API 操作的访问。

   支持以下附加 :ref:`condition keys <minio-policy-conditions>`：

   .. code-block:: shell

      s3:prefix
      s3:delimiter
      s3:max-keys

.. policy-action:: s3:PutObjectVersionTagging

   控制对 :s3-api:`PutObjectVersionTagging <API_PutObjectVersionTagging.html>` S3 API 操作的访问。

   支持以下附加 :ref:`condition keys <minio-policy-conditions>`：

   .. code-block:: shell

      s3:versionid
      s3:ExistingObjectTag/<key>
      s3:RequestObjectTagKeys
      s3:RequestObjectTag/<key>

.. policy-action:: s3:GetObjectVersionTagging

   控制对 :s3-api:`GetObjectVersionTagging <API_GetObjectVersionTagging.html>` S3 API 操作的访问。

   支持以下附加 :ref:`condition keys <minio-policy-conditions>`：

   .. code-block:: shell

      s3:versionid
      s3:ExistingObjectTag/<key>

.. policy-action:: s3:DeleteObjectVersionTagging

   控制对 :s3-api:`DeleteObjectVersionTagging <API_DeleteObjectVersionTagging.html>`  S3 API 操作的访问。

   支持以下附加 :ref:`condition keys <minio-policy-conditions>`：

   .. code-block:: shell

      s3:versionid
      s3:ExistingObjectTag/<key>


.. policy-action:: s3:GetObjectVersion

   控制对 :s3-api:`GetObjectVersion <API_GetObjectVersion.html>`  S3 API 操作的访问。


   支持以下附加 :ref:`condition keys <minio-policy-conditions>`：

   .. code-block:: shell

      s3:versionid
      s3:ExistingObjectTag/<key>

.. policy-action:: s3:BypassGovernanceRetention

   控制对处于 :mc-cmd:`GOVERNANCE <mc retention set MODE>` 保留模式下锁定对象的以下 S3 API 操作的访问：

   - ``s3:PutObjectRetention``
   - ``s3:PutObject``
   - ``s3:DeleteObject``

   更多信息请参见 S3 文档中的 :s3-docs:`s3:BypassGovernanceRetention <object-lock-managing.html#object-lock-managing-bypass>`。

   支持以下附加 :ref:`condition keys <minio-policy-conditions>`：

   .. code-block:: shell

      s3:versionid
      s3:object-lock-remaining-retention-days
      s3:object-lock-retain-until-date
      s3:object-lock-mode
      s3:object-lock-legal-hold
      s3:RequestObjectTagKeys
      s3:RequestObjectTag/<key>

.. policy-action:: s3:PutObjectRetention

   控制对 :s3-api:`PutObjectRetention <API_PutObjectRetention.html>`  S3 API 操作的访问。

   对于任何指定了 :ref:`保留元数据 <minio-object-locking>` 的 ``PutObject`` 操作，都需要此权限。

   支持以下附加 :ref:`condition keys <minio-policy-conditions>`：

   .. code-block:: shell

      s3:x-amz-server-side-encryption
      s3:x-amz-server-side-encryption-customer-algorithm
      s3:x-amz-object-lock-remaining-retention-days
      s3:x-amz-object-lock-retain-until-date
      s3:x-amz-object-lock-mode
      s3:versionid

.. policy-action:: s3:GetObjectRetention

   控制对 :s3-api:`GetObjectRetention <API_GetObjectRetention.html>` S3 API 操作的访问。

   若要在 ``GetObject`` 或 ``HeadObject`` 操作的响应中包含 :ref:`对象锁定元数据 <minio-object-locking>`，则需要此权限。

   支持以下附加 :ref:`condition keys <minio-policy-conditions>`：

   .. code-block:: shell

      s3:x-amz-server-side-encryption
      s3:x-amz-server-side-encryption-customer-algorithm
      s3:versionid

.. policy-action:: s3:GetObjectLegalHold

   控制对 :s3-api:`GetObjectLegalHold <API_GetObjectLegalHold.html>` S3 API 操作的访问。

   若要在 ``GetObject`` 或 ``HeadObject`` 操作的响应中包含 :ref:`对象锁定元数据 <minio-object-locking>`，则需要此权限。

.. policy-action:: s3:PutObjectLegalHold

   控制对 :s3-api:`PutObjectLegalHold <API_PutObjectLegalHold.html>` S3 API 操作的访问。

   对于任何指定了 :ref:`legal hold 元数据 <minio-object-locking>` 的 ``PutObject`` 操作，都需要此权限。

   支持以下附加 :ref:`condition keys <minio-policy-conditions>`：

   .. code-block:: shell

      s3:x-amz-server-side-encryption
      s3:x-amz-server-side-encryption-customer-algorithm
      s3:object-lock-legal-hold
      s3:versionid

.. policy-action:: s3:GetBucketObjectLockConfiguration

   控制对 :s3-api:`GetObjectLockConfiguration <API_GetObjectLockConfiguration.html>` S3 API 操作的访问。

.. policy-action:: s3:PutBucketObjectLockConfiguration

   控制对 :s3-api:`PutObjectLockConfiguration <API_PutObjectLockConfiguration.html>` S3 API 操作的访问。

存储桶通知
~~~~~~~~~~

.. policy-action:: s3:GetBucketNotification

   控制对 :s3-api:`GetBucketNotification <API_GetBucketNotification.html>` S3 API 操作的访问。

.. policy-action:: s3:PutBucketNotification

   控制对 :s3-api:`PutBucketNotification <API_PutBucketNotification.html>` S3 API 操作的访问。

.. policy-action:: s3:ListenNotification

   用于控制与 MinIO 存储桶通知 相关 API 操作的 MinIO 扩展。

   此 action **不** 用于其他兼容 S3 的服务。

.. policy-action:: s3:ListenBucketNotification

   用于控制与 MinIO 存储桶通知 相关 API 操作的 MinIO 扩展。

   此 action **不** 用于其他兼容 S3 的服务。

对象生命周期管理
~~~~~~~~~~~~~~~~

.. policy-action:: s3:PutLifecycleConfiguration

   控制对 :s3-api:`PutLifecycleConfiguration <API_PutBucketLifecycleConfiguration.html>` S3 API 操作的访问。

.. policy-action:: s3:GetLifecycleConfiguration

   控制对 :s3-api:`GetLifecycleConfiguration <API_GetBucketLifecycleConfiguration.html>` S3 API 操作的访问。

对象加密
~~~~~~~~

.. policy-action:: s3:PutEncryptionConfiguration

   控制对 :s3-api:`PutEncryptionConfiguration <API_PutBucketEncryption.html>` S3 API 操作的访问。

.. policy-action:: s3:GetEncryptionConfiguration

   控制对 :s3-api:`GetEncryptionConfiguration <API_GetBucketEncryption.html>` S3 API 操作的访问。

存储桶复制
~~~~~~~~~~

.. policy-action:: s3:GetReplicationConfiguration

   控制对 :s3-api:`GetBucketReplication <API_GetBucketReplication.html>` S3 API 操作的访问。

.. policy-action:: s3:PutReplicationConfiguration

   控制对 :s3-api:`PutBucketReplication <PutBucketReplication.html>` S3 API 操作的访问。

.. policy-action:: s3:ReplicateObject

   用于控制与 :ref:`服务器端存储桶复制 <minio-bucket-replication-serverside>` 相关 API 操作的 MinIO 扩展。

   MinIO 服务器端复制需要此权限。

   支持以下附加 :ref:`condition keys <minio-policy-conditions>`：

   .. code-block:: shell

     s3:versionid
     s3:ExistingObjectTag/<key>

.. policy-action:: s3:ReplicateDelete

   用于控制与 :ref:`服务器端存储桶复制 <minio-bucket-replication-serverside>` 相关 API 操作的 MinIO 扩展。

   作为 MinIO 服务器端复制的一部分，在同步 :ref:`删除操作 <minio-object-delete>` 时需要此权限。

   支持以下附加 :ref:`condition keys <minio-policy-conditions>`：

   .. code-block:: shell

      s3:versionid
      s3:ExistingObjectTag/<key>

.. policy-action:: s3:ReplicateTags

   用于控制与 :ref:`服务器端存储桶复制 <minio-bucket-replication-serverside>` 相关 API 操作的 MinIO 扩展。

   MinIO 服务器端复制需要此权限。

   支持以下附加 :ref:`condition keys <minio-policy-conditions>`：

   .. code-block:: shell

      s3:versionid
      s3:ExistingObjectTag/<key>

.. policy-action:: s3:GetObjectVersionForReplication

   用于控制与 :ref:`服务器端存储桶复制 <minio-bucket-replication-serverside>` 相关 API 操作的 MinIO 扩展。

   MinIO 服务器端复制需要此权限。

   支持以下附加 :ref:`condition keys <minio-policy-conditions>`：

   .. code-block:: shell

      s3:versionid
      s3:ExistingObjectTag/<key>

.. _minio-policy-conditions:
.. _minio-selected-conditional-actions:

受支持的 S3 策略条件键
----------------------

MinIO 策略文档支持 IAM :iam-docs:`条件语句 <reference_policies_elements_condition.html>`。

每个条件元素都由 :iam-docs:`operators <reference_policies_elements_condition_operators.html>` 和条件键组成。MinIO 支持 IAM 条件键的一个子集。
有关任何列出条件键的完整信息，请参见 :iam-docs:`IAM Condition Element Documentation <reference_policies_elements_condition.html>`

对于所有受支持的
:ref:`actions <minio-policy-actions>`，MinIO 支持以下条件键：

- ``aws:Referer``
- ``aws:SourceIp``
- ``aws:UserAgent``
- ``aws:SecureTransport``
- ``aws:CurrentTime``
- ``aws:EpochTime``
- ``aws:PrincipalType``
- ``aws:userid``
- ``aws:username``
- ``x-amz-content-sha256``
- ``s3:signatureAge``

.. warning::

   ``aws:Referer``、``aws:SourceIp`` 和 ``aws.UserAgent`` 键很容易被伪造，因此存在潜在安全风险。
   MinIO 建议仅将这些条件键作为辅助安全措施用于 *拒绝* 访问。

   **绝不要** 仅凭这三个键授予访问权限。

对于特定 S3 action 支持的其他键，请参见该 action 的参考文档。

MinIO 扩展条件键
~~~~~~~~~~~~~~~~

MinIO 在 S3 标准条件键基础上扩展了以下键：

``sts:DurationSeconds``

   .. versionadded:: MinIO SERVER RELEASE.2024-02-06T21-36-22Z

   指定一个以秒为单位的时间，用于限制由 :ref:`minio-sts-assumerolewithwebidentity` 生成的 *所有* Security Token Service 凭证的有效期。

   此值会覆盖客户端指定的 ``DurationSeconds`` 字段。

   例如：

   .. code-block:: json

      {
         "Version": "2012-10-17",
         "Statement": [
            {
                  "Effect": "Allow",
                  "Action": [
                     "sts:AssumeRoleWithWebIdentity"
                  ],
                  "Condition": {
                     "NumericLessThanEquals": {
                        "sts:DurationSeconds": "300"
                     }
                  }
            }
         ]
      }

.. _minio-policy-mc-admin-actions:

``mc admin`` 策略 Action 键
---------------------------

MinIO 支持以下 action，用于为 :mc:`mc admin` 操作定义策略。
这些 action *仅* 对 MinIO 部署有效，*不* 用于其他兼容 S3 的服务：

.. policy-action:: admin:*

   所有 admin action 键的选择器。

.. policy-action:: admin:Heal

   允许执行 heal 命令

.. policy-action:: admin:StorageInfo

   允许列出服务器信息

.. policy-action:: admin:DataUsageInfo

   允许列出数据使用信息

.. policy-action:: admin:TopLocksInfo

   允许列出 top locks

.. policy-action:: admin:Profiling

   允许 profiling

.. policy-action:: admin:ServerTrace

   允许列出 server trace

.. policy-action:: admin:ConsoleLog

   允许在终端列出 console log

.. policy-action:: admin:KMSCreateKey

   允许创建新的 KMS 主密钥

   虽然此选项仍受支持，但更推荐使用 :policy-action:`kms:CreateKey`。

.. policy-action:: admin:KMSKeyStatus

   允许获取 KMS 密钥状态

   虽然此选项仍受支持，但更推荐使用 :policy-action:`kms:KeyStatus`。

.. policy-action:: admin:ServerInfo

   允许列出服务器信息

.. policy-action:: admin:OBDInfo

   允许获取集群 on-board diagnostics

.. policy-action:: admin:ServerUpdate

   允许更新 MinIO 二进制文件

.. policy-action:: admin:ServiceRestart

   允许重启 MinIO 服务。

.. policy-action:: admin:ServiceStop

   允许停止 MinIO 服务。

.. policy-action:: admin:ConfigUpdate

   允许管理 MinIO 配置

.. policy-action:: admin:CreateUser

   允许创建 MinIO 用户

.. policy-action:: admin:DeleteUser

   允许删除 MinIO 用户

.. policy-action:: admin:ListUsers

   允许列出用户

.. policy-action:: admin:EnableUser

   允许启用用户

.. policy-action:: admin:DisableUser

   允许禁用用户

.. policy-action:: admin:GetUser

   允许对用户信息执行 GET

.. policy-action:: admin:AddUserToGroup

   允许将用户添加到组

.. policy-action:: admin:RemoveUserFromGroup

   允许将用户从组中移除

.. policy-action:: admin:GetGroup

   允许获取组信息

.. policy-action:: admin:ListGroups

   允许列出组

.. policy-action:: admin:EnableGroup

   允许启用组

.. policy-action:: admin:DisableGroup

   允许禁用组

.. policy-action:: admin:CreatePolicy

   允许创建策略

.. policy-action:: admin:DeletePolicy

   允许删除策略

.. policy-action:: admin:GetPolicy

   允许获取策略

.. policy-action:: admin:AttachUserOrGroupPolicy

   允许将策略附加到用户/组

.. policy-action:: admin:ListUserPolicies

   允许列出用户策略

.. policy-action:: admin:CreateServiceAccount

   允许创建 MinIO Access Key

.. policy-action:: admin:UpdateServiceAccount

   允许更新 MinIO Access Key

.. policy-action:: admin:RemoveServiceAccount

   允许删除 MinIO Access Key

.. policy-action:: admin:ListServiceAccounts

   允许列出 MinIO Access Key

.. policy-action:: admin:SetBucketQuota

   允许设置存储桶配额

.. policy-action:: admin:GetBucketQuota

   允许获取存储桶配额

.. policy-action:: admin:SetBucketTarget

   允许设置存储桶目标

.. policy-action:: admin:GetBucketTarget

   允许获取存储桶目标

.. policy-action:: admin:SetTier

   允许使用 :mc:`mc ilm tier` 命令创建和修改远程存储层。

.. policy-action:: admin:ListTier

   允许使用 :mc:`mc ilm tier` 命令列出已配置的远程存储层。

.. policy-action:: admin:BandwidthMonitor

   允许获取与当前带宽消耗相关的指标。

.. policy-action:: admin:Prometheus

   允许访问 MinIO :ref:`metrics <minio-metrics-and-alerts>`。
   仅当 MinIO 要求采集指标时进行认证才需要此权限。

.. policy-action:: admin:ListBatchJobs

   允许访问并列出活动中的批处理作业。

.. policy-action:: admin:DescribeBatchJob

   允许访问并查看正在运行的批处理作业的定义详情。

.. policy-action:: admin:StartBatchJob

   允许用户启动批处理作业运行。

.. policy-action:: admin:CancelBatchJob

   允许用户停止当前正在执行的批处理作业。

.. policy-action:: admin:Rebalance

   允许访问并启动、查询或停止跨不同可用存储空间池的对象重平衡。

KMS 策略 action 键
------------------

MinIO 支持通过策略限制密钥管理服务 (KMS) action。

可以在策略中使用以下任一 KMS action 来限制 KMS 活动：

.. policy-action:: kms:Status

   检查 KMS 状态。

.. policy-action:: kms:Metrics

   获取 Prometheus 格式指标。

.. policy-action:: kms:API

   列出受支持的 API 端点。

.. policy-action:: kms:Version

   获取 KMS 版本。

.. policy-action:: kms:CreateKey

   创建新的 KMS 密钥。

.. policy-action:: kms:ListKeys

   获取现有 KMS 密钥列表。

.. policy-action:: kms:KeyStatus

   获取指定 KMS 密钥的状态。

若要选择所有可用的 kms 策略 action，可使用 ``kms:*``。

.. versionchanged:: RELEASE.2024-07-16T23-46-41Z

   KMS action 可以按资源或资源前缀进行限制。
   可以使用通配符 ``*`` 将 KMS action 策略应用到所有匹配该前缀的资源。

   例如，以下策略文档允许用户列出密钥、创建新密钥，并检查任何以 ``keys-abc-`` 或 ``myuser-`` 开头资源上的密钥状态。

   .. code-block:: shell
      :class: copyable

      {
          "Version": "2012-10-17",
          "Statement": [
              {
                  "Effect": "Allow",
                  "Action": [
                      "kms:CreateKey",
                      "kms:KeyStatus",
                      "kms:ListKeys"
                  ],
                  "Resource": [
                      "arn:minio:kms:::keys-abc-*",
                      "arn:minio:kms:::myuser-*"
                  ]
              }
          ]
      }

``mc admin`` 策略条件键
-----------------------

MinIO 支持以下条件，用于为
:mc:`mc admin` :ref:`actions <minio-policy-mc-admin-actions>` 定义策略。

- ``aws:Referer``
- ``aws:SourceIp``
- ``aws:UserAgent``
- ``aws:SecureTransport``
- ``aws:CurrentTime``
- ``aws:EpochTime``

有关任何列出条件键的完整信息，请参见 :iam-docs:`IAM Condition Element Documentation <reference_policies_elements_condition.html>`。

策略变量
--------

MinIO 支持使用策略变量，将来自已认证用户和/或操作的上下文自动替换到分配给该用户的一个或多个策略中。
使用 ``${POLICYVARIABLE}`` 格式在策略的 ``Condition`` 或 ``Resource`` 定义中指定变量。
MinIO 策略变量的工作方式类似于 :iam-docs:`AWS IAM policy elements: Variables and tags <reference_policies_variables.html>`。

每种 MinIO :ref:`identity provider <minio-authentication-and-identity-management>` 都支持其各自的一组策略变量：

- :ref:`minio-policy-variables-internal`
- :ref:`minio-policy-variables-oidc`
- :ref:`minio-policy-variables-ad-ldap`

.. _minio-policy-variables-internal:

MinIO 策略变量
~~~~~~~~~~~~~~

下表列出了用于授权 :ref:`MinIO-managed users <minio-internal-idp>` 的推荐策略变量：

.. list-table::
   :header-rows: 1
   :widths: 40 60
   :width: 100%

   * - 变量
     - 说明

   * - :iam-docs:`aws:referrer <reference_policies_condition-keys.html#condition-keys-referer>`
     - 已认证 API 调用的 HTTP 头中的 referrer。

   * - :iam-docs:`aws:SourceIp <reference_policies_condition-keys.html#condition-keys-sourceip>`
     - 已认证 API 调用的 HTTP 头中的源 IP。

   * - :iam-docs:`aws:username <reference_policies_condition-keys.html#condition-keys-username>`
     - 与已认证 API 调用关联的用户名。

例如，以下策略使用变量将已认证用户的用户名替换到 ``Resource`` 字段中，使该用户只能访问与其用户名匹配的那些前缀：

.. code-block:: json

   {
   "Version": "2012-10-17",
   "Statement": [
         {
            "Action": ["s3:ListBucket"],
            "Effect": "Allow",
            "Resource": ["arn:aws:s3:::mybucket"],
            "Condition": {"StringLike": {"s3:prefix": ["${aws:username}/*"]}}
         },
         {
            "Action": [
            "s3:GetObject",
            "s3:PutObject"
            ],
            "Effect": "Allow",
            "Resource": ["arn:aws:s3:::mybucket/${aws:username}/*"]
         }
      ]
   }

MinIO 会将 ``Resource`` 字段中的 ``${aws:username}`` 变量替换为用户名。
随后 MinIO 对策略进行求值，并授予或撤销对所请求 API 和资源的访问。

.. _minio-policy-variables-oidc:

OpenID 策略变量
~~~~~~~~~~~~~~~

.. include:: /includes/common/common-minio-oidc.rst
   :start-after: start-minio-oidc-policy-variables
   :end-before: end-minio-oidc-policy-variables

.. _minio-policy-variables-ad-ldap:

Active Directory / LDAP 策略变量
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

下表列出了用于授权 :ref:`AD/LDAP users <minio-external-identity-management-ad-ldap>` 的受支持策略变量：

.. list-table::
   :header-rows: 1
   :widths: 40 60
   :width: 100%

   * - 变量
     - 说明

   * - ``ldap:username``
     - 已认证用户的简单用户名（``name``）。
         这不同于用户的 DistinguishedName 或 CommonName。

   * - ``ldap:user``
     - 已认证用户使用的 Distinguished Name。

   * - ``ldap:groups``
     - 已认证用户的组 Distinguished Name。

例如，以下策略使用变量将已认证用户的 ``name`` 替换到 ``Resource`` 字段中，使该用户只能访问与其名称匹配的那些前缀：

.. code-block:: json

   {
   "Version": "2012-10-17",
   "Statement": [
         {
            "Action": ["s3:ListBucket"],
            "Effect": "Allow",
            "Resource": ["arn:aws:s3:::mybucket"],
            "Condition": {"StringLike": {"s3:prefix": ["${ldap:username}/*"]}}
         },
         {
            "Action": [
            "s3:GetObject",
            "s3:PutObject"
            ],
            "Effect": "Allow",
            "Resource": ["arn:aws:s3:::mybucket/${ldap:username}/*"]
         }
      ]
   }

MinIO 会将 ``Resource`` 字段中的 ``${ldap:username}`` 变量替换为已认证用户的 ``name`` 值。
随后 MinIO 对策略进行求值，并授予或撤销对所请求 API 和资源的访问。
