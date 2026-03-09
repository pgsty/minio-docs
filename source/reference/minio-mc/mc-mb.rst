=========
``mc mb``
=========

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc mb

语法
------

.. start-mc-mb-desc

:mc:`mc mb` 命令在指定路径创建新的存储桶或目录。

.. end-mc-mb-desc

你也可将 :mc:`mc mb` 用于本地文件系统，实现与 ``mkdir -p``
命令行工具类似的效果。

.. tab-set::

   .. tab-item:: 示例

      以下命令会在 ``myminio`` MinIO 部署上创建一个新的存储桶 ``mydata``。
      该命令创建的存储桶将 :ref:`启用对象锁定 <minio-object-locking>`。

      .. code-block:: shell
         :class: copyable

         mc mb --with-locks myminio/mydata

   .. tab-item:: 语法

      命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] mb                   \
                          [--ignore-existing]  \
                          [--region "string"]  \
                          [--with-lock]        \
                          [--with-versioning]  \
                          ALIAS

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


参数
~~~~~~~~~~

.. mc-cmd:: ALIAS
   :required:

   要在其上创建新存储桶的 MinIO 或其他 S3 兼容服务。

   如果要在 MinIO 上创建存储桶，请指定
   :ref:`alias <alias>` 和存储桶名称。例如：

   .. code-block:: none

      mc mb play/mybucket

   如果要在本地文件系统上创建目录，请指定该目录的完整
   路径。例如：

   .. code-block:: none

      mc mb ~/mydata/mydir

.. mc-cmd:: --ignore-existing, p
   :optional:

   指示 :mc:`mc mb` 在存储桶或目录已存在时不执行任何操作。

.. mc-cmd:: --region
   :optional:

   指定创建存储桶的区域。
   如果指定的 :mc-cmd:`~mc mb ALIAS` 是文件系统目录，则该选项无效。

   如果未指定，默认值为 ``us-east-1``。

.. mc-cmd:: --with-lock, l
   :optional:

   在指定存储桶上启用 :ref:`对象锁定 <minio-object-locking>`。
   对象锁定要求并因此隐含启用对象版本控制。

   .. important::

      你*只能*在创建存储桶时启用对象锁定。
      未启用对象锁定创建的存储桶无法使用
      :ref:`存储桶生命周期管理 <minio-lifecycle-management>` 或
      :ref:`存储桶对象锁定 <minio-object-locking>` 功能。

.. mc-cmd:: --with-versioning
   :optional:

   在新存储桶上启用 :ref:`对象版本控制 <minio-bucket-versioning>`。
   启用版本控制后，默认情况下 MinIO 允许每个对象的版本数最多达到 Int64 最大值，即超过 9.2 quintillion。
   可定义 :ref:`对象过期 <minio-lifecycle-management-create-expiry-rule>` 规则，清理不再需要的对象版本，例如按版本数量或版本日期删除。

   :ref:`存储桶复制 <minio-bucket-replication>` 或 :ref:`站点复制 <minio-site-replication-overview>` 需要版本控制。
   版本控制不隐含也不要求对象锁定。

全局标志
~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
--------

创建启用对象锁定的存储桶
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc:`mc mb` 在 S3 兼容主机上创建存储桶。
:mc-cmd:`~mc mb --with-lock` 选项创建启用锁定的存储桶：

.. code-block:: shell
   :class: copyable

   mc mb --with-lock ALIAS/BUCKET

- 将 :mc-cmd:`ALIAS <mc mb ALIAS>` 替换为 S3 兼容主机的 :mc:`alias <mc alias>`。

- 将 :mc-cmd:`BUCKET <mc mb ALIAS>` 替换为要创建的存储桶。

在指定区域创建新存储桶
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc:`mc mb` 在 S3 兼容主机上创建存储桶。
:mc-cmd:`~mc mb --region` 选项在目标区域创建该存储桶。

.. code-block:: shell
   :class: copyable

   mc mb --region --region=us-west-2 myminio/mynewbucket

上述命令会在 ``us-west-2`` 区域中的 ``myminio`` 上创建新存储桶 ``mynewbucket``。

创建启用版本控制的新存储桶
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: shell
   :class: copyable

   mc mb --with-versioning myminio/myversionedbucket

上述命令会在 ``myminio`` alias 上创建新的存储桶 ``myversionedbucket``。
新存储桶为桶内所有对象启用 :ref:`对象版本控制 <minio-bucket-versioning>`。

行为
--------

每个部署的存储桶限制
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

MinIO 不限制你在单个部署上可创建的存储桶数量。
但作为通用指导，MinIO 建议每个部署的存储桶数量不超过 500,000。

非 MinIO S3 服务的存储桶限制
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

某些 S3 服务可能限制单个用户或账户可创建的存储桶数量。
例如，Amazon S3 将每个账户限制为
:s3-docs:`100 buckets <BucketRestrictions.html>`。如果用户在目标 S3 服务上达到存储桶上限，:mc:`mc mb` 可能返回错误。

MinIO 对象存储部署不限制每个用户可创建的
存储桶数量。

在创建存储桶时启用对象锁定
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

MinIO 遵循
:s3-docs:`AWS S3 behavior <object-lock-overview.html#object-lock-bucket-config>`，
要求你*必须*在创建存储桶时启用 :ref:`对象锁定 <minio-object-locking>`。
未启用对象锁定创建的存储桶*永远*无法启用对象保留或锁定。

启用存储桶锁定并*不会*设置任何对象锁定或保留配置。
建议将启用存储桶锁定作为标准实践。

S3 兼容性
~~~~~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
