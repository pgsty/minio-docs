.. _minio-mc-ilm-rule-edit:

====================
``mc ilm rule edit``
====================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc ilm rule edit

.. versionchanged:: RELEASE.2022-12-24T15-21-38Z

   ``mc ilm rule edit`` 替代 ``mc ilm edit``。

语法
----

.. start-mc-ilm-rule-edit-desc

:mc:`mc ilm rule edit` 命令用于修改 MinIO 存储桶上现有的对象生命周期管理规则。

.. end-mc-ilm-rule-edit-desc

.. tab-set::

   .. tab-item:: 示例

      以下命令用于修改 ``myminio`` 部署上 ``mydata`` 存储桶的现有生命周期管理规则：

      .. code-block:: shell
         :class: copyable

         mc ilm rule edit --id "c79ntj94b0t6rukh6lr0" --expiry-days 90  myminio/mydata
         
         mc ilm rule edit --id "c79nu2p4b0t6qko19rgg" --expired-object-delete-marker myminio/mydata

         mc ilm rule edit --id "c79n19dn10dnab109fg1" --transition-days 30 --tier "COLDTIER"
         
      该命令按如下方式修改指定规则：

      - 删除超过 90 天的对象。
      - 如果对象没有其他剩余版本，则删除 ``DeleteMarker`` 墓碑标记。
      - 将超过 30 天的对象迁移到 ``COLDTIER`` 远程层。

   .. tab-item:: 语法

      命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] ilm rule edit                                       \
                          --id "string"                                       \
                          [--prefix "string"]                                 \
                          [--enable]                                          \
                          [--disable]                                         \
                          [--expire-all-object-versions]                      \
                          [--expire-days "string"]                            \
                          [--expire-delete-marker]                            \
                          [--transition-days "string"]                        \
                          [--transition-tier "string"]                        \
                          [--noncurrent-expire-days "string"]                 \
                          [--noncurrent-expire-newer "string"]                \
                          [--noncurrent-transition-days "string"]             \
                          [--noncurrent-transition-tier "string"]             \
                          [--tags]                                            \
                          ALIAS

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   要修改对象生命周期管理规则的 MinIO 部署中，存储桶的 :ref:`alias <alias>` 和完整路径。
   例如：

   .. code-block:: none

      mc ilm rule edit myminio/mydata

.. mc-cmd:: --id
   :required:

   规则的唯一 ID。使用 :mc:`mc ilm rule ls` 列出存储桶规则，并获取要修改规则的 ``id``。

.. mc-cmd:: --disable
   :optional:

   停用该规则，但保留该规则以供后续使用。
   当规则被禁用时，对象不会迁移或过期。

.. mc-cmd:: --enable
   :optional:

   启用规则以迁移或使对象过期。

.. mc-cmd:: --prefix
   :optional:
   
   将管理规则限制为特定的存储桶前缀。
   
   例如：

   .. code-block:: none

      mc ilm rule edit --prefix "meetingnotes/" myminio/mydata --expire-days "90"

   该命令会修改一条规则：对于 ``myminio`` ALIAS 中 ``mydata`` 存储桶内前缀为 ``meetingnotes/`` 的任意对象，在 90 天后过期。


.. mc-cmd:: --expire-all-object-versions
   :optional:   

   .. versionadded:: mc RELEASE.2024-02-24T01-33-20Z

   使对象的所有当前版本和非当前版本都过期。
   与 :mc-cmd:`~mc ilm rule add --expire-days` 选项结合使用，可指定扫描器进程在多少天后删除对象的所有版本。

   在 :ref:`scanner <minio-concepts-scanner>` 处理该命令后，部署中不会保留该对象的任何版本。

   .. versionadded:: MinIO RELEASE.2024-05-01T01-11-10Z

   该标志*仅*适用于最新版本**不**是 delete marker 的对象。


.. mc-cmd:: --expire-days
   :optional:

   对象创建后保留的天数。达到指定天数后，MinIO 会将对象标记为待删除。

   使用该选项时请谨慎，其行为可能导致已上传对象立即过期。任何在指定过期日期*之后*
   创建的对象都会自动满足过期条件。同样地，若指定的日历日期*早于*当前系统主机时间，
   该规则覆盖的所有对象都会被标记为删除。指定日历日期过后，请考虑立即移除使用该选项的 ILM 规则。

   对于启用版本控制的存储桶，过期规则仅适用于*当前*
   对象版本。使用
   :mc-cmd:`~mc ilm rule edit --noncurrent-expire-days` 选项
   可将过期行为应用于非当前对象版本。

   MinIO 使用 :ref:`scanner process <minio-concepts-scanner>` 根据所有已配置的
   生命周期管理规则检查对象。高 IO 负载或系统资源受限导致的扫描变慢，可能会延迟
   生命周期管理规则的生效。更多信息请参阅 :ref:`minio-lifecycle-management-scanner`。

   与以下选项互斥：

   - :mc-cmd:`~mc ilm rule edit --expire-delete-marker`

.. mc-cmd:: --expire-delete-marker
   :optional:

   指定该选项可让 MinIO 删除没有剩余对象版本的对象的 delete marker。
   具体来说，delete marker 是给定对象*唯一*剩余的“版本”。

   该选项与以下选项互斥：
   
   - :mc-cmd:`~mc ilm rule edit --tags`
   - :mc-cmd:`~mc ilm rule edit --expire-days`

   MinIO 使用 :ref:`scanner process <minio-concepts-scanner>` 根据所有已配置的
   生命周期管理规则检查对象。高 IO 负载或系统资源受限导致的扫描变慢，可能会延迟
   生命周期管理规则的生效。更多信息请参阅 :ref:`minio-lifecycle-management-scanner` 和 :ref:`minio-object-delete`。

.. mc-cmd:: --noncurrent-expire-days
   :optional:

   对象版本变为
   *non-current*（即该对象的另一个版本成为当前 `HEAD`）后保留的天数。
   达到指定天数后，MinIO 会将非当前对象版本标记为待删除。

   该选项的行为与
   S3 ``NoncurrentVersionExpiration`` 操作一致。

   MinIO 使用 :ref:`scanner process <minio-concepts-scanner>` 根据所有已配置的
   生命周期管理规则检查对象。高 IO 负载或系统资源受限导致的扫描变慢，可能会延迟
   生命周期管理规则的生效。更多信息请参阅 :ref:`minio-lifecycle-management-scanner`。

.. mc-cmd:: --noncurrent-expire-newer
   :optional:

   在应用过期前要保留的对象 non-current 版本数量。
   超出指定数量的较旧 non-current 版本会过期。
   
   默认情况下，当过期规则生效时，MinIO 不保留任何 non-current 版本。

.. mc-cmd:: --noncurrent-transition-days
   :optional:

   对象处于 non-current 状态（即被该对象更新版本替换）后经过多少天，
   MinIO 将该对象版本标记为可迁移。系统主机时间到达该日历日期后，
   MinIO 会将对象迁移到
   :mc-cmd:`~mc ilm rule edit --transition-tier` 指定的
   远程存储层。

   该选项对未启用版本控制的存储桶无效。需要指定
   :mc-cmd:`~mc ilm rule edit --noncurrent-transition-tier`。

   该选项的行为与
   S3 ``NoncurrentVersionTransition`` 操作一致。

   如果远程层是另一个 MinIO 部署，可将值设置为 ``0``，使新对象立即满足迁移到远程层的条件。

   MinIO 使用 :ref:`scanner process <minio-concepts-scanner>` 根据所有已配置的
   生命周期管理规则检查对象。高 IO 负载或系统资源受限导致的扫描变慢，可能会延迟
   生命周期管理规则的生效。更多信息请参阅 :ref:`minio-lifecycle-management-scanner`。


.. mc-cmd:: --noncurrent-transition-tier
   :optional:

   MinIO :ref:`transitions noncurrent objects versions <minio-lifecycle-management-tiering>` 所使用的远程存储层。
   指定一个通过 :mc:`mc ilm tier add` 创建的远程存储层。

   MinIO *不会*自动将对象从先前指定的远程层迁移到新的远程层。
   MinIO 会继续将存储在旧远程层上的对象请求路由到旧远程层。


.. mc-cmd:: --tags
   :optional:

   一个或多个以 ``&`` 分隔的键值对，用于描述要应用生命周期配置规则的对象标签。

   该选项与以下选项互斥：

   - :mc-cmd:`~mc ilm rule edit --expire-delete-marker`

.. mc-cmd:: --transition-days
   :optional:
   
   对象创建后经过多少个日历天，MinIO 会将对象标记为可迁移。
   MinIO 会将对象迁移到
   :mc-cmd:`~mc ilm rule edit --transition-tier` 指定的远程存储层。
   以整数指定天数，例如 ``30`` 表示 30 天。
   如果远程层是另一个 MinIO 部署，可将值设置为 ``0``，使新对象立即满足迁移到远程层的条件。

   对于启用版本控制的存储桶，迁移规则仅适用于*当前*
   对象版本。使用
   :mc-cmd:`~mc ilm rule edit --noncurrent-transition-days` 选项
   可将迁移行为应用于非当前对象版本。

   需要指定 :mc-cmd:`~mc ilm rule edit --transition-tier`。

   MinIO 使用 :ref:`scanner process <minio-concepts-scanner>` 根据所有已配置的
   生命周期管理规则检查对象。高 IO 负载或系统资源受限导致的扫描变慢，可能会延迟
   生命周期管理规则的生效。更多信息请参阅 :ref:`minio-lifecycle-management-scanner`。

.. mc-cmd:: --transition-tier
   :optional:

   MinIO :ref:`transition objects <minio-lifecycle-management-tiering>` 所使用的远程存储层。
   指定一个通过 :mc:`mc ilm tier add` 创建的远程存储层。

   如果指定 :mc-cmd:`~mc ilm rule edit --transition-days`，则该选项必填。

   MinIO *不会*自动将对象从先前指定的远程层迁移到新的远程层。
   MinIO 会继续将存储在旧远程层上的对象请求路由到旧远程层。

全局参数
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

修改现有生命周期管理规则
~~~~~~~~~~~~~~~~~~~~~~~~

使用带有 :mc-cmd:`~mc ilm rule edit --id` 的 :mc:`mc ilm rule edit` 修改现有对象过期规则：

.. code-block:: shell
   :class: copyable

   mc ilm rule edit ALIAS/PATH --id "RULEID" [FLAGS]

- 将 :mc-cmd:`ALIAS <mc ilm rule edit ALIAS>` 替换为 S3 兼容主机的
  :mc:`alias <mc alias>`。

- 将 :mc-cmd:`PATH <mc ilm rule edit ALIAS>` 替换为 S3 兼容主机上存储桶的路径。

- 将 ``RULEID`` 替换为对象生命周期管理规则的唯一 ID。
  使用 :mc:`mc ilm rule ls` 查找 ``RULEID``。

- 指定任意附加标志以新增或修改生命周期管理规则。
  例如，指定
  :mc-cmd:`~mc ilm rule edit --transition-days` 以覆盖该规则现有的迁移天数值。

禁用生命周期管理规则
~~~~~~~~~~~~~~~~~~~~

使用带有 :mc-cmd:`~mc ilm rule edit --disable` 的 :mc:`mc ilm rule edit` 停止使用现有管理规则。

.. code-block:: shell
   :class: copyable
   
   mc ilm rule edit --id "RULEID" --disable myminio/mybucket

- 将 ``RULEID`` 替换为对象生命周期管理规则的唯一 ID。
  使用 :mc:`mc ilm rule ls` 查找 ``RULEID``。
- 将 ``myminio`` 替换为规则所在部署的 ALIAS。
- 将 ``mybucket`` 替换为规则对应的存储桶。

所需权限
--------

有关编辑规则所需的权限，请参阅父命令中的 :ref:`required permissions <minio-mc-ilm-rule-permissions>`。


行为
----

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
