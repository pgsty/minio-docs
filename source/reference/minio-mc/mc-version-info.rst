.. _minio-mc-version-info:

===================
``mc version info``
===================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc version info


语法
----

.. start-mc-version-info-desc

:mc:`mc version info` 命令返回指定存储桶的版本控制状态。

.. end-mc-version-info-desc

.. tab-set::

   .. tab-item:: 示例

      以下命令返回 ``myminio`` MinIO 部署中 ``mybucket`` 存储桶的版本控制状态：

      .. code-block:: shell
         :class: copyable

         mc version info myminio/mybucket

   .. tab-item:: 语法

      该命令的语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] version info ALIAS

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


参数
~~~~

.. mc-cmd:: ALIAS

   要获取其版本控制状态的存储桶的完整路径。
   例如：

   .. code-block:: shell

      mc version info myminio/mybucket


全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals


示例
----

获取存储桶版本控制状态
~~~~~~~~~~~~~~~~~~~~~~

使用 :mc:`mc version info` 获取存储桶的版本控制状态：

.. code-block:: shell
   :class: copyable

   mc version info ALIAS/PATH

- 将 :mc-cmd:`ALIAS <mc version info ALIAS>` 替换为已配置 MinIO 部署的 :mc:`alias <mc alias>`。

- 将 :mc-cmd:`PATH <mc version info ALIAS>` 替换为要获取版本控制状态的存储桶。


行为
----

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
