==================
``mc quota clear``
==================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc quota clear

.. versionchanged:: RELEASE.2022-12-13T00-23-28Z

   ``mc quota clear`` 替代了 ``mc admin bucket quota --clear``。

.. versionchanged:: RELEASE.2024-07-31T15-58-33Z

   ``mc quota clear`` 已弃用。

说明
----

.. start-mc-quota-clear-desc

:mc-cmd:`mc quota clear` 命令会移除存储桶上已配置的存储配额。

.. end-mc-quota-clear-desc


示例
----


清除已配置的存储桶配额
~~~~~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc quota clear` 标志可从存储桶中移除配额。

.. code-block:: shell
   :class: copyable

   mc quota clear TARGET/BUCKET

- 将 ``TARGET`` 替换为已配置 MinIO 部署的 :mc-cmd:`alias <mc alias>`。
  将 ``BUCKET`` 替换为要清除配额的存储桶名称。

语法
----

:mc-cmd:`mc quota clear` 的语法如下：

.. code-block:: shell
   :class: copyable

   mc quota clear TARGET [ARGUMENTS]

:mc-cmd:`mc quota clear` 支持以下参数：

.. mc-cmd:: TARGET
   :required:

   该命令为其创建配额的存储桶完整路径。
   指定 MinIO 部署的 :mc-cmd:`alias <mc alias>` 作为路径前缀。
   例如：

   .. code-block:: shell
      :class: copyable

      mc quota clear play/mybucket


全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals


S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
