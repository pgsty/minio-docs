.. _minio-mc-version-suspend:

======================
``mc version suspend``
======================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc version suspend


语法
----

.. start-mc-version-suspend-desc

:mc:`mc version suspend` 命令用于禁用指定存储桶上的版本控制。

.. end-mc-version-suspend-desc

.. tab-set::

   .. tab-item:: 示例

      以下命令会为 ``myminio`` MinIO 部署中的 ``mybucket`` 存储桶禁用版本控制：

      .. code-block:: shell
         :class: copyable

         mc version suspend myminio/mybucket

   .. tab-item:: 语法

      该命令的语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] version suspend ALIAS

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


参数
~~~~

.. mc-cmd:: ALIAS

   要禁用版本控制的存储桶完整路径。
   例如：

   .. code-block:: shell

      mc version suspend myminio/mybucket


全局参数
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals


示例
----

禁用存储桶版本控制
~~~~~~~~~~~~~~~~~~

使用 :mc:`mc version suspend` 为存储桶禁用版本控制：

.. code-block:: shell
   :class: copyable

   mc version suspend ALIAS/PATH

- 将 :mc-cmd:`ALIAS <mc version suspend ALIAS>` 替换为已配置 MinIO 部署的 :mc:`alias <mc alias>`。

- 将 :mc-cmd:`PATH <mc version suspend ALIAS>` 替换为要禁用版本控制的存储桶。


行为
----

现有数据下的存储桶版本控制
~~~~~~~~~~~~~~~~~~~~~~~~~~

在包含现有版本化数据的存储桶上禁用版本控制时，*不会* 删除任何已版本化对象。
应用程序在禁用存储桶版本控制后仍可继续访问版本化数据。
使用 :mc-cmd:`mc rm --versions ALIAS/BUCKET/OBJECT <mc rm --versions>` 删除某个对象及其所有版本。


S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
