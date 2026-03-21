=================
``mc quota info``
=================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc quota info

.. versionchanged:: RELEASE.2022-12-13T00-23-28Z

   ``mc quota info`` 替代了 ``mc admin bucket quota``。

.. versionchanged:: RELEASE.2024-07-31T15-58-33Z

   ``mc quota info`` 已弃用。

说明
----

.. start-mc-quota-info-desc

:mc-cmd:`mc quota info` 命令显示存储桶当前配置的配额。

.. end-mc-quota-info-desc

示例
----


获取存储桶配额配置
~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc quota info` 获取存储桶当前的配额配置：

.. code-block:: shell
   :class: copyable

   mc quota info TARGET/BUCKET

将 ``TARGET`` 替换为已配置 MinIO 部署的 :mc-cmd:`alias <mc alias>`。
将 ``BUCKET`` 替换为要查询配额的存储桶名称。

语法
----

:mc-cmd:`mc quota info` 的语法如下：

.. code-block:: shell
   :class: copyable

   mc quota info TARGET

:mc-cmd:`mc quota info` 支持以下参数：

.. mc-cmd:: TARGET
   :required:

   要为其创建配额的存储桶完整路径。
   指定 MinIO 部署的 :mc-cmd:`alias <mc alias>` 作为路径前缀。
   例如：

   .. code-block:: shell
      :class: copyable

      mc quota play/mybucket

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
