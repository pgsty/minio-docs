.. _minio-object-locking:
.. _minio-object-retention:

====================
MinIO 对象锁定
====================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. container:: extlinks-video

   - `Object locking and retention overview <https://youtu.be/Hk9Z-sltUu8?ref=docs>`__
   - `Object locking and retention lab <https://youtu.be/thNus-DL1u4?ref=docs>`__

概述
----

MinIO 对象锁定（“对象保留”）通过强制执行一次写入、多次读取（WORM）不可变性，防止 :ref:`已启用版本控制的对象 <minio-bucket-versioning>` 被删除。MinIO 同时支持
:ref:`基于时长的对象保留 <minio-object-locking-retention-modes>`
和
:ref:`无限期 legal hold 保留 <minio-object-locking-legalhold>`。

根据
`Cohasset Associates <https://min.io/cohasset?ref-docs>`__ 的说明，MinIO 对象锁定 提供关键的数据保留合规能力，并满足 SEC17a-4(f)、FINRA 4511(C) 和 CFTC 1.31(c)-(d) 的要求。

.. card-carousel:: 1

   .. card:: 未启用锁定的存储桶

      .. image:: /images/retention/minio-versioning-delete-object.svg
         :alt: 删除对象
         :align: center
         :width: 600px

      MinIO 版本控制会保留对象变更的完整历史。
      但应用仍可显式删除特定对象版本。

   .. card:: 启用锁定的存储桶

      .. image:: /images/retention/minio-object-locking.svg
         :alt: 锁定 30 天的对象
         :align: center
         :width: 600px

      对存储桶中的对象应用默认 30 天的 WORM 锁，可确保所有对象版本在最短保留期内受到保护。

   .. card:: 锁定存储桶中的删除操作

      .. image:: /images/retention/minio-object-locking-delete.svg
         :alt: 锁定存储桶中的删除操作
         :align: center
         :width: 600px

      :ref:`删除操作 <minio-object-delete>` 在
      :ref:`已启用版本控制的存储桶 <minio-bucket-versioning-delete>` 中遵循常规行为，即 MinIO 会为对象创建 ``DeleteMarker``。不过，对象中非 Delete Marker 的版本仍受保留规则约束，可防止任何针对特定版本的删除或覆盖尝试。

   .. card:: 锁定存储桶中的版本删除操作

      .. image:: /images/retention/minio-object-locking-delete-version.svg
         :alt: 锁定存储桶中的版本删除操作
         :align: center
         :width: 600px

      对于受 WORM 锁定保护的特定对象版本，MinIO 会阻止任何
      :ref:`删除 <minio-object-delete>` 尝试。客户端最早只能在锁过期后删除该版本。

MinIO 对象锁定在功能和 API 层面与 AWS S3
:s3-docs:`兼容 <object-lock.html>`。
本页概述了 MinIO 中对象锁定/保留的实现概念。更多信息请参见 AWS S3 文档
:s3-docs:`How S3 Object Lock works <object-lock.html>`。

按照
:s3-docs:`S3 的行为 <object-lock-overview.html#object-lock-bucket-config>`，
只能在创建存储桶时启用对象锁定。对于创建时未启用锁定的存储桶，之后无法再启用对象锁定。启用后，你可以在任意时刻配置对象保留规则。对象锁定依赖 :ref:`版本控制 <minio-bucket-versioning>`，并会隐式启用该特性。

.. _minio-bucket-locking-interactions-versioning:

与版本控制的交互
~~~~~~~~~~~~~~~~

受 WORM 锁定保护的对象在锁过期或被显式解除之前均不可变更。锁定以对象版本为粒度，每个版本都独立保持不可变。

如果应用对受锁定对象执行未指定版本的删除操作，该操作会生成一个 :ref:`Delete Marker <minio-bucket-versioning-delete>`。
任何显式删除受 WORM 锁定对象的尝试都会报错失败。Delete Marker *不* 受 WORM 锁定保护。
更多信息请参见 S3 文档
:s3-docs:`Managing delete markers and object lifecycles
<object-lock-managing.html#object-lock-managing-lifecycle>`。

例如，考虑以下默认启用了
:ref:`minio-object-locking-governance` 锁定的存储桶：

.. code-block:: shell

   $ mc ls --versions play/locking-guide

     [DATETIME]    29B 62429eb1-9cb7-4dc5-b507-9cc23d0cc691 v3 PUT data.csv
     [DATETIME]    32B 78b3105a-02a1-4763-8054-e66add087710 v2 PUT data.csv
     [DATETIME]    23B c6b581ca-2883-41e2-9905-0a1867b535b8 v1 PUT data.csv

由于对象锁定设置，对 ``data.csv`` 的*特定版本*执行删除会失败：

.. code-block:: shell

   $ mc rm --version-id 62429eb1-9cb7-4dc5-b507-9cc23d0cc691 play/data.csv

     Removing `play/locking-guide/data.csv` (versionId=62429eb1-9cb7-4dc5-b507-9cc23d0cc691).
     mc: <ERROR> Failed to remove `play/locking-guide/data.csv`. 
         Object, 'data.csv (Version ID=62429eb1-9cb7-4dc5-b507-9cc23d0cc691)' is 
         WORM protected and cannot be overwritten

对 ``data.csv`` 执行未指定版本的删除会成功，并为对象创建一个新的 ``DeleteMarker``：

.. code-block:: shell

   $ mc rm play/locking-guide/data.csv

     [DATETIME]     0B acce329f-ad32-46d9-8649-5fe8bf4ec6e0 v4 DEL data.csv
     [DATETIME]    29B 62429eb1-9cb7-4dc5-b507-9cc23d0cc691 v3 PUT data.csv
     [DATETIME]    32B 78b3105a-02a1-4763-8054-e66add087710 v2 PUT data.csv
     [DATETIME]    23B c6b581ca-2883-41e2-9905-0a1867b535b8 v1 PUT data.csv

与生命周期管理的交互
~~~~~~~~~~~~~~~~~~~~

对于受过期规则覆盖的对象，MinIO :ref:`对象过期 <minio-lifecycle-management-expiration>`
会遵循所有生效中的对象锁定和保留设置。

- 对于仅作用于*当前*对象版本的过期规则，
  MinIO 会为受锁定对象创建一个 Delete Marker。

- 对于作用于*非当前对象版本*的过期规则，
  MinIO 只能在保留期结束*之后*，或保留已被显式解除时（例如 legal hold）对非当前版本执行过期。

例如，考虑以下默认启用了
:ref:`minio-object-locking-governance` 且保留期为 45 天的存储桶：

.. code-block:: shell

   $ mc ls --versions play/locking-guide

     [7D]    29B 62429eb1-9cb7-4dc5-b507-9cc23d0cc691 v3 PUT data.csv
     [30D]    32B 78b3105a-02a1-4763-8054-e66add087710 v2 PUT data.csv
     [60D]    23B c6b581ca-2883-41e2-9905-0a1867b535b8 v1 PUT data.csv

为超过 7 天的*当前*对象创建过期规则时，会为该对象生成一个 Delete Marker：

.. code-block:: shell

   $ mc ls --versions play/locking-guide

     [0D]     0B acce329f-ad32-46d9-8649-5fe8bf4ec6e0 v4 DEL data.csv
     [7D]    29B 62429eb1-9cb7-4dc5-b507-9cc23d0cc691 v3 PUT data.csv
     [30D]    32B 78b3105a-02a1-4763-8054-e66add087710 v2 PUT data.csv
     [60D]    23B c6b581ca-2883-41e2-9905-0a1867b535b8 v1 PUT data.csv

不过，对于超过 7 天的*非当前*对象，过期规则只有在已配置的 WORM 锁过期*之后*才会生效。由于该存储桶设置了 45 天的 ``GOVERNANCE`` 保留，因此只有 ``data.csv`` 的 ``v1`` 版本已解锁，因而可被删除。

教程
----

创建启用对象锁定的存储桶
~~~~~~~~~~~~~~~~~~~~~~~~

按照 S3 的行为，必须在创建存储桶时启用对象锁定。
你可以使用 MinIO :mc:`mc` CLI 或 S3 兼容 SDK 创建启用了对象锁定的存储桶。

使用带有 :mc-cmd:`~mc mb --with-lock`
选项的 :mc:`mc mb` 命令创建启用了对象锁定的存储桶：

.. code-block:: shell
   :class: copyable

   mc mb --with-lock ALIAS/BUCKET

- 将 ``ALIAS`` 替换为已配置 MinIO 部署的 :mc:`alias <mc alias>`。

- 将 ``BUCKET`` 替换为要创建的存储桶
  :mc-cmd:`名称 <mc mb ALIAS>`。

配置存储桶默认对象保留
~~~~~~~~~~~~~~~~~~~~~~

你可以使用 MinIO :mc:`mc` CLI 或 S3 兼容 SDK 配置对象锁定规则（“对象保留”）。

MinIO 同时支持设置存储桶默认保留规则和按对象设置保留规则。
以下示例演示的是存储桶默认保留。对于按对象设置保留，请参见所选 SDK 中相应 ``PUT`` 操作的文档。


使用带有
:mc-cmd:`--recursive <mc retention set --recursive>` 和
:mc-cmd:`--default <mc retention set --default>` 选项的
:mc:`mc retention set` 命令，为存储桶设置默认保留模式：

.. code-block:: shell
   :class: copyable

   mc retention set --recursive --default MODE DURATION ALIAS/BUCKET

- 将 :mc-cmd:`MODE <mc retention set MODE>` 替换为 :ref:`COMPLIANCE <minio-object-locking-compliance>` 或 :ref:`GOVERNANCE <minio-object-locking-governance>`。

- 将 :mc-cmd:`DURATION <mc retention set VALIDITY>` 替换为对象锁定持续生效的时长。

- 将 :mc-cmd:`ALIAS <mc retention set ALIAS>` 替换为已配置 MinIO 部署的 :mc:`alias <mc alias>`。

- 将 :mc-cmd:`BUCKET <mc retention set ALIAS>` 替换为要设置默认保留规则的存储桶名称。

启用 Legal Hold 保留
~~~~~~~~~~~~~~~~~~~~

你可以使用 MinIO :mc:`mc` CLI 或 S3 兼容 SDK，为对象启用或禁用无限期 legal hold 保留。

你可以对已经处于 :ref:`COMPLIANCE <minio-object-locking-compliance>` 或 :ref:`GOVERNANCE <minio-object-locking-governance>` 锁定下的对象设置 legal hold。
即使保留锁过期，对象在 legal hold 生效期间仍会保持 WORM 锁定。
你或其他拥有必要权限的用户必须显式解除 legal hold，才能移除 WORM 锁。

使用 :guilabel:`mc legalhold set` 命令切换对象的 legal hold 状态。

.. code-block:: shell
   :class: copyable

   mc legalhold set ALIAS/PATH

- 将 :mc-cmd:`ALIAS <mc legalhold set ALIAS>` 替换为已配置 MinIO 部署的 :mc:`alias <mc alias>`。

- 将 :mc-cmd:`PATH <mc legalhold set ALIAS>` 替换为要启用 legal hold 的对象路径。

.. _minio-object-locking-retention-modes:

对象保留模式
------------

MinIO 实现了以下
:s3-docs:`S3 对象锁定模式 <object-lock-overview.html>`：

.. list-table::
   :header-rows: 1
   :widths: 40 60
   :width: 100%

   * - 模式
     - 说明

   * - :ref:`minio-object-locking-governance`
     - 阻止非特权用户执行任何会变更对象或其锁定设置的操作。
       
       在存储桶或对象上拥有 :policy-action:`s3:BypassGovernanceRetention` 权限的用户，可以修改对象或其锁定设置。

       在配置的保留规则持续时间结束后，MinIO 会自动解除该锁。

   * - :ref:`minio-object-locking-compliance`
     - 阻止任何会变更对象或其锁定设置的操作。
       
       包括 :ref:`MinIO root <minio-users-root>` 用户在内的任何 MinIO 用户都无法修改对象或其设置。

       在配置的保留规则持续时间结束后，MinIO 会自动解除该锁。

.. _minio-object-locking-governance:

GOVERNANCE 模式
~~~~~~~~~~~~~~~

处于 ``GOVERNANCE`` 锁定下的对象可防止非特权用户执行写操作。

``GOVERNANCE`` 锁定对象提供受管控的不可变性。拥有 :policy-action:`s3:BypassGovernanceRetention` 操作权限的用户可以修改被锁定对象、调整保留时长，或完全解除该锁。绕过 ``GOVERNANCE`` 保留还要求在请求中设置 ``x-amz-bypass-governance-retention:true`` header。

MinIO 的 ``GOVERNANCE`` 锁在功能上与
:s3-docs:`S3 GOVERNANCE 模式
<object-lock.html#object-lock-retention-modes>` 完全一致。

.. _minio-object-locking-compliance:

COMPLIANCE 模式
~~~~~~~~~~~~~~~

处于 ``COMPLIANCE`` 锁定下的对象可防止*所有*用户执行写操作，包括 :ref:`MinIO root <minio-users-root>` 用户。

``COMPLIANCE`` 锁定对象提供完全不可变性。
在配置的保留时长结束之前，无法更改或移除该锁。

MinIO 的 ``COMPLIANCE`` 锁在功能上与
:s3-docs:`S3 COMPLIANCE 模式
<object-lock.html#object-lock-retention-modes>` 完全一致。

.. _minio-object-locking-legalhold:

Legal Hold
----------

处于 legal hold 状态下的对象可防止*所有*用户执行写操作，包括 :ref:`MinIO root <minio-users-root>` 用户。

legal hold 为无限期，并对锁定对象强制执行完全不可变性。
只有拥有 :policy-action:`s3:PutObjectLegalHold` 权限的特权用户才能设置或解除 legal hold。

legal hold 在对象级别生效。
如果你为一组对象启用 legal hold，例如某个存储桶中的现有内容，则该存储桶后续新创建的对象不会受到影响。

legal hold 与 :ref:`minio-object-locking-governance` 和 :ref:`minio-object-locking-compliance` 保留设置是互补关系。
同时受 legal hold 和 ``GOVERNANCE/COMPLIANCE`` 保留规则保护的对象，会持续保持 WORM 锁定，直到 legal hold 被解除*且*规则到期。

对于 ``GOVERNANCE`` 锁定对象，即使用户拥有绕过保留的必要权限，legal hold 仍会阻止其修改该对象。
