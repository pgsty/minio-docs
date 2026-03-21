.. _minio-lifecycle-management:

===========================
对象生命周期管理
===========================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 1

.. container:: extlinks-video

   - `MinIO Object Lifecycle Management Part I <https://youtu.be/Exg2KsfzHzI?ref=docs>`__
   - `MinIO Object Lifecycle Management Part II <https://youtu.be/5fz3rE3wjGg?ref=docs>`__
   - `MinIO Object Lifecycle Management Lab <https://youtu.be/5fz3rE3wjGg?ref=docs>`__

使用 MinIO 对象生命周期管理，可以创建基于时间或日期的规则，自动迁移或过期对象。
对于对象迁移，MinIO 会自动将对象移动到已配置的远程存储层。
对于对象过期，MinIO 会自动删除该对象。

为了兼容将工作负载和生命周期规则从 S3 迁移到 MinIO 的场景，MinIO 的行为和语法遵循 :s3-docs:`S3 lifecycle <object-lifecycle-mgmt.html>`。
例如，您可以导出 S3 生命周期管理规则并将其导入 MinIO，反之亦然。
MinIO 使用 JSON 描述生命周期管理规则，因此在导入 S3 生命周期规则时，可能需要在 XML 与 JSON 之间进行转换。

.. _minio-lifecycle-management-tiering:

对象迁移（"Tiering"）
-----------------------------

MinIO 支持创建对象迁移生命周期管理规则，使 MinIO 可以自动将对象移动到远程存储“层”。
MinIO 支持以下任意一种远程层目标：

- :ref:`MinIO <minio-lifecycle-management-transition-to-minio>`
- :ref:`Amazon S3 <minio-lifecycle-management-transition-to-s3>`
- :ref:`Google Cloud Storage <minio-lifecycle-management-transition-to-gcs>`
- :ref:`Microsoft Azure Blob Storage 
  <minio-lifecycle-management-transition-to-azure>`

MinIO 对象迁移适用于这类场景：将私有云或公有云基础设施中 MinIO 集群里的陈旧数据迁移到低成本的私有云或公有云存储方案中。
目录对象，即名称以 ``/`` 结尾的 0 字节对象，不会被分层迁移。
MinIO 会按需管理已分层对象的取回，无需应用端添加额外逻辑。

使用 :mc:`mc ilm tier add` 命令为分层数据创建远程目标。
然后，您可以使用 :mc-cmd:`mc ilm rule add --transition-days` 命令，在指定的日历天数后将对象迁移到该层。

.. versionadded:: RELEASE.2022-11-10T18-20-21Z

您可以对存储桶或存储桶前缀使用 :mc:`mc ls`，验证对象的分层状态。
输出中会包含每个对象的存储层：

.. code-block:: shell

   $ mc ls play/mybucket
   [2022-11-08 11:30:24 PST]    52MB  STANDARD log-data.csv
   [2022-11-09 12:20:18 PST]    120MB WARM event-2022-11-09.mp4

- ``STANDARD`` 表示对象存储在 MinIO 部署上。
- ``WARM`` 表示对象存储在同名远程层上。

.. important::

   MinIO 对象迁移支持这类成本优化策略：将较旧或陈旧的数据移动到成本优化的远程存储层，例如云存储或高密度 HDD 存储。

   MinIO 对象迁移**不**提供备份与恢复功能。
   在 MinIO 发生数据丢失时，您不能将远程层用作恢复源。

   如需支持备份/恢复或 :abbr:`BC/DR (Business Continuity / Disaster Recovery)` 需求，请使用 :ref:`site replication <minio-site-replication-overview>` 或 :ref:`bucket replication <minio-bucket-replication>`。

对远程数据的独占访问
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. include:: /includes/common-minio-tiering.rst
   :start-after: start-transition-bucket-access-desc
   :end-before: end-transition-bucket-access-desc

远程数据的可用性
~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. include:: /includes/common-minio-tiering.rst
   :start-after: start-transition-data-loss-desc
   :end-before: end-transition-data-loss-desc

启用版本控制的存储桶
~~~~~~~~~~~~~~~~~~~~

对于已启用 :ref:`版本控制 <minio-bucket-versioning>` 的存储桶迁移规则，MinIO 采用 :s3-docs:`S3 behavior <intro-lifecycle-rules.html#intro-lifecycle-rules-actions>`。
具体来说，MinIO 默认将迁移操作应用于对象的*当前*版本。

如需迁移对象的非当前版本，请在创建迁移规则时指定 :mc-cmd:`~mc ilm rule add --noncurrent-transition-days` 和 :mc-cmd:`~mc ilm rule add --noncurrent-transition-tier` 选项。

.. _minio-lifecycle-management-expiration:

对象过期
-----------------

MinIO 生命周期管理支持对存储桶中的对象执行过期操作。
对象“过期”是指对该对象执行 ``DELETE`` 操作。
例如，您可以创建生命周期管理规则，使所有超过 365 天的对象过期。

.. todo: Diagram of MinIO Expiration

使用 :mc-cmd:`mc ilm rule add --expire-days` 可以让对象在指定的日历天数后过期。

对于已配置 :ref:`replication <minio-bucket-replication>` 的存储桶，MinIO 不会复制由生命周期管理过期规则删除的对象。
更多信息请参见 :ref:`minio-replication-behavior-delete`。

启用版本控制的存储桶
~~~~~~~~~~~~~~~~~~~~

对于已启用 :ref:`版本控制 <minio-bucket-versioning>` 的存储桶过期规则，MinIO 采用 :s3-docs:`S3 behavior <intro-lifecycle-rules.html#intro-lifecycle-rules-actions>`。
对于启用版本控制的存储桶，MinIO 具有以下默认行为：

- MinIO 仅将过期选项应用于对象的*当前*版本，具体方式是像版本化删除的常规行为一样创建 ``DeleteMarker``。

  如需让对象的非当前版本过期，请在创建过期规则时指定 :mc-cmd:`~mc ilm rule add --noncurrent-expire-days` 选项。

- MinIO 不会让 ``DeleteMarkers`` 过期，即使该对象已不存在其他版本。

  如需在该对象已无剩余版本时让删除标记过期，请在创建过期规则时指定 :mc-cmd:`~mc ilm rule add --expire-delete-marker` 选项。

- 如需让一个没有删除标记的对象在指定天数后其所有版本都过期，请将 :mc-cmd:`~mc ilm rule add --expire-all-object-versions` 标志与 :mc-cmd:`~mc ilm rule add --expire-days` 标志一同使用。
  这允许该对象在指定天数过去后被永久删除。

  .. versionchanged:: MinIO RELEASE.2024-05-01T01-11-10Z

     此标志仅适用于没有删除标记的对象。

.. _minio-lifecycle-management-scanner:

生命周期管理对象扫描器
-----------------------------------

MinIO 使用内置的 :ref:`scanner <minio-concepts-scanner>` 主动检查对象是否符合所有已配置的生命周期管理规则。

扫描器是一个低优先级进程，在 :abbr:`I/O (Input / Output)` 负载较高时会让出资源，以避免因规则触发时机导致性能尖峰。
因此，扫描器可能要到生命周期规则周期已经过去*之后*，才会检测到某个对象已满足配置的迁移或过期生命周期规则条件。

.. toctree::
   :hidden:

   /administration/object-management/transition-objects-to-minio.rst
   /administration/object-management/transition-objects-to-s3.rst
   /administration/object-management/transition-objects-to-gcs.rst
   /administration/object-management/transition-objects-to-azure.rst
   /administration/object-management/create-lifecycle-management-expiration-rule.rst
