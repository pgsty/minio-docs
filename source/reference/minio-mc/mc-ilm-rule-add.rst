.. _minio-mc-ilm-rule-add:

===================
``mc ilm rule add``
===================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc ilm rule add

.. versionchanged:: RELEASE.2022-12-24T15-21-38Z

   ``mc ilm rule rm`` 替代 ``mc ilm add``。


语法
------

.. start-mc-ilm-rule-add-desc

:mc:`mc ilm rule add` 命令用于向存储桶添加对象生命周期管理规则。

.. end-mc-ilm-rule-add-desc

该命令支持添加 :ref:`迁移（分层） <minio-lifecycle-management-tiering>` 和 :ref:`过期 <minio-lifecycle-management-expiration>` 两类生命周期管理规则。

.. tab-set::

   .. tab-item:: 示例

      以下命令将新的生命周期管理规则添加到 ``myminio`` 部署上的 ``mydata`` 存储桶：

      .. code-block:: shell
         :class: copyable

         mc ilm rule add --expire-days 90 --noncurrent-expire-days 30  myminio/mydata
         
         mc ilm rule add --expire-delete-marker myminio/mydata

         mc ilm rule add --transition-days 30 --transition-tier "COLDTIER" myminio/mydata
         
         mc ilm rule add --noncurrent-transition-days 7 --noncurrent-transition-tier "COLDTIER" 

      已配置规则的效果如下：

      - 删除超过 90 天的对象
      - 对象变为非当前版本后 30 天删除
      - 如果对象没有其他剩余版本，则删除 ``DeleteMarker`` 墓碑。
      - 将超过 30 天的对象迁移到 ``COLDTIER`` 远程层。
      - 对象变为非当前版本后 7 天迁移到 ``COLDTIER`` 远程层。

   .. tab-item:: 语法

      命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] ilm rule add                               \
                          [--prefix string]                          \
                          [--tags string]                            \
                          [--expire-days "integer"]                  \
                          [--expire-all-object-versions]             \
                          [--expire-delete-marker]                   \
                          [--transition-days "string"]               \
                          [--transition-tier "string"]               \
                          [--noncurrent-expire-days "integer"]       \
                          [--noncurrent-expire-newer "integer"]      \
                          [--noncurrent-transition-days "integer"]   \
                          [--noncurrent-transition-tier "string"]    \
                          [--site-gt "string"]                       \
                          [--size-lt "string"]                       \
                          ALIAS

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~~~~~~~

.. mc-cmd:: ALIAS
   :required:
   
   MinIO 部署上用于添加对象生命周期管理规则的 :ref:`alias <alias>` 和存储桶。
   
   例如：

   .. code-block:: none

      mc ilm rule add myminio/mydata

.. mc-cmd:: --prefix
   :optional:
   
   将管理规则限定到特定对象前缀。
   
   例如：

   .. code-block:: none

      mc ilm rule add --prefix "meetingnotes/" myminio/mydata --expire-days "90"

   该命令会创建一条规则：对于 ``myminio`` ALIAS 上 ``mydata`` 存储桶中具有 ``meetingnotes/`` 前缀的对象，在 90 天后过期。

.. mc-cmd:: --tags
   :optional:

   一个或多个以 ``&`` 分隔的键值对，用于描述对象标签，以筛选生命周期配置规则适用的对象。

   此选项与以下选项互斥：

   - :mc-cmd:`~mc ilm rule add --expire-delete-marker`

.. mc-cmd:: --expire-all-object-versions
   :optional:   

   .. versionadded:: mc RELEASE.2024-02-24T01-33-20Z

   使对象的当前版本和非当前版本全部过期。
   与 :mc-cmd:`~mc ilm rule add --expire-days` 选项结合使用，以指定扫描器进程在多少天后删除对象的所有版本。

   在 :ref:`scanner <minio-concepts-scanner>` 处理该命令后，部署中将不再保留该对象的任何版本。

   .. versionchanged:: MinIO RELEASE.2024-05-01T01-11-10Z

   此标志*仅*适用于最新版本**不是**删除标记的对象。

.. mc-cmd:: --expire-days
   :optional:   

   对象创建后保留的天数。
   达到指定天数后，MinIO 会将对象标记为待删除。
   使用整数指定天数，例如 ``30`` 表示 30 天。

   对于启用版本控制的存储桶，过期规则仅适用于对象的*当前*版本。
   使用 :mc-cmd:`~mc ilm rule add --noncurrent-expire-days` 或 :mc-cmd:`~mc ilm rule add --expire-all-object-versions` 标志，可将过期行为应用于对象的非当前版本。

   MinIO 使用 :ref:`scanner process <minio-concepts-scanner>` 根据所有已配置的生命周期管理规则检查对象。
   在高 IO 负载或系统资源受限时，扫描变慢可能导致生命周期管理规则应用延迟。
   更多信息请参见 :ref:`minio-lifecycle-management-scanner`。

   与以下选项互斥：

   - :mc-cmd:`~mc ilm rule add --expire-delete-marker`

   关于对象过期的完整文档，请参见 :ref:`minio-lifecycle-management-expiration` 和 :ref:`minio-object-delete`。

.. mc-cmd:: --expire-delete-marker
   :optional:

   指定该选项后，MinIO 会移除没有剩余对象版本的对象删除标记。
   具体来说，删除标记是该对象唯一剩余的“版本”。

   此选项与以下选项互斥：
   
   - :mc-cmd:`~mc ilm rule add --tags`
   - :mc-cmd:`~mc ilm rule add --expire-days`

   MinIO 使用 :ref:`scanner process <minio-concepts-scanner>` 根据所有已配置的生命周期管理规则检查对象。
   在高 IO 负载或系统资源受限时，扫描变慢可能导致生命周期管理规则应用延迟。
   更多信息请参见 :ref:`minio-lifecycle-management-scanner`。

   关于对象过期的完整文档，请参见 :ref:`minio-lifecycle-management-expiration` 和 :ref:`minio-object-delete`。

.. mc-cmd:: --transition-days
   :optional:
   
   对象创建后经过多少个日历日，MinIO 将对象标记为可迁移。
   MinIO 会将对象迁移到由 :mc-cmd:`~mc ilm rule add --transition-tier` 指定的远程层。
   使用整数指定天数，例如 ``30`` 表示 30 天。
   如果远程层是另一个 MinIO 部署，可将该值设为 ``0``，使新对象立即具备迁移到远程层的资格。

   对于启用版本控制的存储桶，迁移规则仅适用于对象的*当前*版本。
   使用 :mc-cmd:`~mc ilm rule add --noncurrent-transition-days` 选项可将迁移行为应用于对象的非当前版本。

   需要同时指定 :mc-cmd:`~mc ilm rule add --transition-tier`。

   MinIO 使用 :ref:`scanner process <minio-concepts-scanner>` 根据所有已配置的生命周期管理规则检查对象。
   在高 IO 负载或系统资源受限时，扫描变慢可能导致生命周期管理规则应用延迟。
   更多信息请参见 :ref:`minio-lifecycle-management-scanner`。

   关于对象迁移的完整文档，请参见 :ref:`minio-lifecycle-management-tiering`。

.. mc-cmd:: --transition-tier
   :optional:

   MinIO :ref:`transition objects <minio-lifecycle-management-tiering>` 的目标远程层。
   指定由 :mc:`mc ilm tier add` 创建的现有远程层。

   当指定 :mc-cmd:`~mc ilm rule add --transition-days` 时，此项为必需。

.. mc-cmd:: --noncurrent-expire-days
   :optional:

   对象版本变为*非当前*后（即该对象已有其他版本成为 `HEAD`）保留的天数。
   达到指定天数后，MinIO 会将非当前对象版本标记为待删除。

   此选项与 S3 ``NoncurrentVersionExpiration`` 操作行为一致。

   MinIO 使用 :ref:`scanner process <minio-concepts-scanner>` 根据所有已配置的生命周期管理规则检查对象。
   在高 IO 负载或系统资源受限时，扫描变慢可能导致生命周期管理规则应用延迟。
   更多信息请参见 :ref:`minio-lifecycle-management-scanner`。

.. mc-cmd:: --noncurrent-transition-days
   :optional:

   对象在成为非当前版本后（即被同一对象的更新版本替换）经过多少天，MinIO 将该对象版本标记为可迁移。
   当系统主机日期时间超过该日历日期时，MinIO 会将对象迁移到 :mc-cmd:`~mc ilm rule add --transition-tier` 指定的远程层。

   此选项对非版本化存储桶无效。
   需要同时指定 :mc-cmd:`~mc ilm rule add --noncurrent-transition-tier`。

   此选项与 S3 ``NoncurrentVersionTransition`` 操作行为一致。

   MinIO 使用 :ref:`scanner process <minio-concepts-scanner>` 根据所有已配置的生命周期管理规则检查对象。
   在高 IO 负载或系统资源受限时，扫描变慢可能导致生命周期管理规则应用延迟。
   更多信息请参见 :ref:`minio-lifecycle-management-scanner`。

.. mc-cmd:: --noncurrent-transition-tier
   :optional:

   MinIO :ref:`transitions noncurrent objects versions <minio-lifecycle-management-tiering>` 的目标远程层。
   指定由 :mc:`mc ilm tier add` 创建的远程层。

.. mc-cmd:: --noncurrent-expire-newer
   :optional:

   要保留的非当前对象版本最大数量，按从新到旧排序。
   
   使用此标志可按先进先出方式保留文件的一定数量历史版本。
   在保留达到最大非当前版本数后，MinIO 会将其余更旧的非当前对象版本标记为可过期。
   
   下表基于 ``--noncurrent-expire-newer 3`` 展示对象版本数量及其过期资格：

   .. list-table::
      :widths: 50 50
      :width: 100% 

      * - v5（当前版本）
        - 当前版本不受 ILM 规则影响。
      * - v4
        - 保留
      * - v3
        - 保留
      * - v2
        - 保留
      * - v1
        - 标记为过期

   MinIO 会保留当前版本 v5。
   MinIO 还会从最新版本开始，保留接下来的 ``3`` 个非当前版本。
   这意味着 MinIO 会将 ``v4``、``v3`` 和 ``v2`` 作为要保留的三个非当前版本。

   ``v1`` 是第四个非当前版本，超出非当前版本保留上限，因此 MinIO 会将 ``v1`` 标记为过期。

   更新此标志的数值只会影响尚未标记的对象版本。
   对于已标记为过期的版本，即使增加保留数量也不会改变。

   MinIO 使用 :ref:`scanner process <minio-concepts-scanner>` 根据所有已配置的生命周期管理规则检查对象。
   在高 IO 负载或系统资源受限时，扫描变慢可能导致生命周期管理规则应用延迟。
   更多信息请参见 :ref:`minio-lifecycle-management-scanner`。
   
.. mc-cmd:: --size-gt
   :optional:

   .. versionadded:: mc RELEASE.2023-12-02T02-03-28Z 

   选择大于指定值的对象。
   以数字加单位输入该值，例如 ``5GiB`` 表示 5 gibibytes。

   有效单位包括：

   .. list-table::
      :header-rows: 1
      :widths: 20 80
      :width: 100%
   
      * - 后缀
        - 单位大小
   
      * - ``k``
        - KB（千字节，1000 字节）
   
      * - ``m``
        - MB（兆字节，1000 KB）
   
      * - ``g``
        - GB（吉字节，1000 MB）
   
      * - ``t``
        - TB（太字节，1000 GB）
   
      * - ``ki``
        - KiB（kibibyte，1024 字节）
   
      * - ``mi``
        - MiB（mebibyte，1024 KiB）
   
      * - ``gi``
        - GiB（gibibyte，1024 MiB）
   
      * - ``ti``
        - TiB（tebibyte，1024 GiB）

.. mc-cmd:: --size-lt
   :optional:
  
   .. versionadded:: mc RELEASE.2023-12-02T02-03-28Z

   选择小于指定值的对象。
   以数字加单位输入该值，例如 ``1M`` 表示 1 megabyte。

   有效单位包括：

   .. list-table::
      :header-rows: 1
      :widths: 20 80
      :width: 100%
   
      * - 后缀
        - 单位大小
   
      * - ``k``
        - KB（千字节，1000 字节）
   
      * - ``m``
        - MB（兆字节，1000 KB）
   
      * - ``g``
        - GB（吉字节，1000 MB）
   
      * - ``t``
        - TB（太字节，1000 GB）
   
      * - ``ki``
        - KiB（kibibyte，1024 字节）
   
      * - ``mi``
        - MiB（mebibyte，1024 KiB）
   
      * - ``gi``
        - GiB（gibibyte，1024 MiB）
   
      * - ``ti``
        - TiB（tebibyte，1024 GiB）

全局标志
~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
--------

在指定天数后过期存储桶全部内容
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

将 :mc:`mc ilm rule add` 与 :mc-cmd:`~mc ilm rule add --expire-all-object-versions` 和 :mc-cmd:`~mc ilm rule add --expire-days` 标志配合使用，可在对象创建后经过指定天数后，将存储桶中的所有当前版本和非当前版本内容标记为过期：

.. code-block:: shell
   :class: copyable

   mc ilm rule add ALIAS/PATH --expire-all-object-versions --expire-days "DAYS" 

- 将 :mc-cmd:`ALIAS <mc ilm rule add ALIAS>` 替换为 S3 兼容主机的 :mc:`alias <mc alias>`。

- 将 :mc-cmd:`PATH <mc ilm rule add ALIAS>` 替换为 S3 兼容主机上存储桶的路径。

- 将 :mc-cmd:`DAYS <mc ilm rule add --expire-days>` 替换为每个对象过期前的天数。
  例如，指定 ``30`` 表示对象在创建 30 天后过期。

将某前缀下的非当前对象版本迁移到不同层
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

将 :mc:`mc ilm rule add` 与 :mc-cmd:`~mc ilm rule add --prefix` 和 :mc-cmd:`~mc ilm rule add --transition-tier` 配合使用，可将对象较旧的非当前版本迁移到不同存储层。

.. code-block:: shell
   :class: copyable

   mc ilm rule add --prefix "doc/" --transition-days "90" --transition-tier "MINIOTIER-1"  \
          --noncurrent-transition-days "45" --noncurrent-transition-tier "MINIOTIER-2"    \
          myminio/mybucket

该命令会检查 ``myminio`` 部署上 ``mybucket`` 存储桶中前缀为 ``doc/`` 的内容。

- 前缀下超过 90 天的当前对象会移动到 ``MINIOTIER-1`` 存储层。
- 前缀下超过 45 天的非当前对象会移动到 ``MINIOTIER-2`` 存储层。
- ``MINIOTIER-1`` 和 ``MINIOTIER-2`` 均已通过 :mc:`mc admin tier add` 创建。

让某前缀下所有对象过期，并让当前版本比非当前版本保留更久
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

将 :mc:`mc ilm rule add` 命令与 :mc-cmd:`~mc ilm rule add --prefix`、:mc-cmd:`~mc ilm rule add --expire-days` 和 :mc-cmd:`~mc ilm rule add --noncurrent-expire-days` 配合使用，可让对象的当前版本与非当前版本在不同时间过期。

.. code-block:: shell
   :class: copyable

   mc ilm rule add --prefix "doc/" --expire-days "300" --noncurrent-expire-days "100" myminio/mybucket

该命令会检查 ``myminio`` 部署上 ``mybucket`` 存储桶中前缀为 ``doc/`` 的内容。

- 当前对象在 300 天后过期。
- 非当前对象在 100 天后过期。

迁移前缀 ``/doc`` 下大于 1MiB 的非当前版本
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

将 :mc:`mc ilm rule add` 命令与 :mc-cmd:`~mc ilm rule add --prefix`、:mc-cmd:`~mc ilm rule add --size-gt` 和 :mc-cmd:`~mc ilm rule add --noncurrent-expire-days` 配合使用，可让对象的当前版本与非当前版本在不同时间过期。

.. code-block:: shell
   :class: copyable

   mc ilm rule add --prefix "doc/" --size-gt 1MiB --transition-days "90" --transition-tier "MINIOTIER-1" \
         --noncurrent-transition-days "45" --noncurrent-transition-tier "MINIOTIER-1" \
         myminio/mybucket/

该命令会检查 ``myminio`` 部署上 ``mybucket`` 存储桶中前缀为 ``doc/`` 的内容。

该命令会选择以下对象：

- 大于 1MiB 且超过 90 天的当前对象。
- 大于 1MiB 且超过 45 天的非当前对象。
  
所选对象会迁移到 ``MINIOTIER-1``。

移除删除标记
~~~~~~~~~~~~~~~~~~~~~

以下命令会移除删除标记仅为对象唯一剩余版本的对象的删除标记。

.. code-block:: shell
   :class: copyable

   mc ilm rule add ALIAS/PATH --expire-delete-marker 

- 将 :mc-cmd:`ALIAS <mc ilm rule add ALIAS>` 替换为 S3 兼容主机的 :mc:`alias <mc alias>`。

- 将 :mc-cmd:`PATH <mc ilm rule add ALIAS>` 替换为 S3 兼容主机上存储桶的路径。

.. note::

   若要删除最新版本为删除标记的对象的所有版本，*包括删除标记本身*，可考虑使用 :ref:`batch expiration <minio-mc-batch-generate-expire-job>`。

所需权限
--------------------

有关添加规则所需权限，请参见父命令中的 :ref:`required permissions <minio-mc-ilm-rule-permissions>`。


行为
--------

生命周期管理对象扫描器
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

MinIO 使用 :ref:`scanner process <minio-concepts-scanner>` 根据所有已配置的
生命周期管理规则检查对象。在高 IO 负载或
系统资源受限时，扫描变慢可能导致生命周期管理
规则应用延迟。更多信息请参见 :ref:`minio-lifecycle-management-scanner`。

过期与迁移
~~~~~~~~~~~~~~~~~~~~

MinIO 支持在同一个存储桶或存储桶前缀中同时指定过期和迁移规则。
无论对象的迁移状态如何，MinIO 都可以对其执行过期规则。
使用
:mc:`mc ilm rule ls` 查看当前已配置的对象生命周期
管理规则，以评估过期与迁移规则之间可能的相互影响。

S3 兼容性
~~~~~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
