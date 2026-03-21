==============
``mc version``
==============

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc version

描述
----

.. start-mc-version-desc

:mc:`mc version` 命令可为 MinIO 存储桶启用、禁用并获取 :ref:`版本控制 <minio-bucket-versioning>` 状态。

.. end-mc-version-desc

有关 MinIO 中对象版本控制的更多信息，请参见 :ref:`minio-bucket-versioning`。

:mc:`mc version` 包含以下子命令：

.. list-table::
   :header-rows: 1
   :widths: 30 70
   :width: 100%

   * - 子命令
     - 描述

   * - :mc:`~mc version enable`
     - .. include:: /reference/minio-mc/mc-version-enable.rst
          :start-after: start-mc-version-enable-desc
          :end-before: end-mc-version-enable-desc

   * - :mc:`~mc version info`
     - .. include:: /reference/minio-mc/mc-version-info.rst
          :start-after: start-mc-version-info-desc
          :end-before: end-mc-version-info-desc

   * - :mc:`~mc version suspend`
     - .. include:: /reference/minio-mc/mc-version-suspend.rst
          :start-after: start-mc-version-suspend-desc
          :end-before: end-mc-version-suspend-desc


行为
----

对象锁定会启用存储桶版本控制
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

虽然默认禁用存储桶版本控制，但在存储桶上或该存储桶中的对象上配置对象锁定时，会自动为该存储桶启用版本控制。
有关配置对象锁定的更多信息，请参见 :mc:`mc retention`。

现有数据下的存储桶版本控制
~~~~~~~~~~~~~~~~~~~~~~~~~~

在包含现有数据的存储桶上启用版本控制时，会立即为每个未版本化对象创建一个 null 值版本 ID。

在包含现有已版本化数据的存储桶上禁用版本控制时，*不会* 删除任何已版本化对象。
禁用存储桶版本控制后，应用程序仍可继续访问已版本化数据。
使用 :mc-cmd:`mc rm --versions ALIAS/BUCKET/OBJECT <mc rm --versions>` 删除某个对象 *及其* 所有版本。

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility

.. toctree::
   :titlesonly:
   :hidden:

   /reference/minio-mc/mc-version-enable
   /reference/minio-mc/mc-version-info
   /reference/minio-mc/mc-version-suspend
