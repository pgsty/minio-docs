.. _minio-server-envvar-storage-class:
.. _minio-ec-storage-class:

============
纠删码设置
============

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

本页介绍用于配置写入 MinIO 集群对象时所使用 :ref:`纠删码 <minio-erasure-coding>` :ref:`校验 <minio-ec-parity>` 的相关设置。
这会影响 MinIO 如何使用驱动器空间，以及 MinIO 如何在驱动器丢失或类似问题发生时恢复已存储对象。

.. include:: /includes/common-mc-admin-config.rst
   :start-after: start-minio-settings-defined
   :end-before: end-minio-settings-defined

.. include:: /includes/common-mc-admin-config.rst
   :start-after: start-minio-settings-test-before-prod
   :end-before: end-minio-settings-test-before-prod

.. _minio-ec-storage-class-standard:

标准存储类
----------

.. note::

   *MinIO Storage Classes* 与 *AWS Storage Classes* 不同。

   AWS Storage Classes 指将给定对象存储到的特定存储层级，例如 ``hot`` 或 ``glacier`` 存储。
   MinIO Storage Classes 会影响所使用的纠删码校验设置，并与对象的 :ref:`minio-availability-resiliency` 相关。

   如需在不同类型存储之间分层（例如用于成本管理），请参见 :ref:`minio-lifecycle-management-tiering`。

.. tab-set::

   .. tab-item:: Environment Variable
      :sync: envvar

      .. envvar:: MINIO_STORAGE_CLASS_STANDARD

   .. tab-item:: Configuration Setting
      :sync: config

      .. mc-conf:: storage_class standard
         :delimiter: " "

该部署的 :ref:`parity level <minio-ec-parity>`。
MinIO 会使用该校验值对采用默认 ``STANDARD`` 存储类写入的对象进行分片。

MinIO 会参考请求元数据中的 ``x-amz-storage-class`` 头，以确定应为对象分配哪个存储类。
设置该头的具体语法或方式取决于与 MinIO 服务器交互时所使用的方法。

请使用 ``EC:M`` 记法指定该值，其中 ``M`` 表示要为对象创建的校验块数量。

下表列出了基于部署中初始服务器池 :ref:`erasure set size <minio-ec-erasure-set>` 的默认值：

.. list-table::
   :header-rows: 1
   :widths: 30 70
   :width: 100%

   * - 纠删集合大小
     - 默认校验 (EC:N)

   * - 1
     - EC:0

   * - 2-3
     - EC:1

   * - 4-5
     - EC:2

   * - 6 - 7
     - EC:3

   * - 8 - 16
     - EC:4

支持的最小值是 ``0``，表示不提供纠删码保护。
此类部署的可用性/韧性完全依赖底层存储控制器或存储资源。

最大值取决于部署中初始服务器池的纠删集合大小，其上限为 :math:`\frac{\text{ERASURE_SET_SIZE}}{\text{2}}`。
例如，纠删集合条带大小为 16 的部署，其标准校验最大值为 8。

可在启动后将该值更改为 ``0`` 与该纠删集合大小上限之间的任意值。
MinIO 只会将变更后的校验值应用于新写入对象。
现有对象会保留其创建时的校验值。

降低冗余存储类
--------------

.. note::

   *MinIO Storage Classes* 与 *AWS Storage Classes* 不同。

   AWS Storage Classes 指将给定对象存储到的特定存储层级，例如 ``hot`` 或 ``glacier`` 存储。
   MinIO Storage Classes 会影响所使用的纠删码校验设置，并与对象的 :ref:`minio-availability-resiliency` 相关。

   如需在不同类型存储之间分层（例如用于成本管理），请参见 :ref:`minio-lifecycle-management-tiering`。

.. tab-set::

   .. tab-item:: Environment Variable
      :sync: envvar

      .. envvar:: MINIO_STORAGE_CLASS_RRS

   .. tab-item:: Configuration Setting
      :sync: config

      .. mc-conf:: storage_class rrs
         :delimiter: " "

用于以 ``REDUCED`` 存储类写入对象的 :ref:`parity level <minio-ec-parity>`。

MinIO 会参考请求元数据中的 ``x-amz-storage-class`` 头，以确定应为对象分配哪个存储类。
设置该头的具体语法或方式取决于与 MinIO 服务器交互时所使用的方法。

请使用 ``EC:M`` 记法指定该值，其中 ``M`` 表示要为对象创建的校验块数量。

该值**必须**小于或等于 :envvar:`MINIO_STORAGE_CLASS_STANDARD`。

对于纠删集合大小小于 2 的部署，不能设置此值。
纠删集合大小大于 1 的部署默认值为 ``EC:1``。
纠删集合大小为 1 的部署默认值为 ``EC:0``。

校验保留优化
------------

.. tab-set::

   .. tab-item:: Environment Variable
      :sync: envvar

      .. envvar:: MINIO_STORAGE_CLASS_OPTIMIZE

   .. tab-item:: Configuration Setting
      :sync: config

      .. mc-conf:: storage_class optimize
         :delimiter: " "

默认情况下，如果目标纠删集合维持写入法定人数（write quorum）*但*有一个或多个驱动器离线，MinIO 会自动“升级”对象的校验。
该行为有助于确保给定对象与写入健康纠删集合的对象保持相同可用性。

将该设置指定为 ``capacity``，可指示 MinIO 不为对象创建任何额外校验。
这会优先保障集群总体容量，但代价是当该纠删集合中更多驱动器故障时，对象可用性可能降低。

注释
----

.. tab-set::

   .. tab-item:: Environment Variable
      :sync: envvar

      .. envvar:: MINIO_STORAGE_CLASS_COMMENT

   .. tab-item:: Configuration Setting
      :sync: config

      .. include:: /includes/common-mc-admin-config.rst
         :start-after: start-minio-settings-no-config-option
         :end-before: end-minio-settings-no-config-option

为存储类设置添加注释。
