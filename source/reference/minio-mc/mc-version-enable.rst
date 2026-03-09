.. _minio-mc-version-enable:

=====================
``mc version enable``
=====================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc version enable


语法
----

.. start-mc-version-enable-desc

:mc:`mc version enable` 命令用于为指定存储桶启用版本控制。

.. end-mc-version-enable-desc

.. tab-set::

   .. tab-item:: 示例

      以下命令为 ``myminio`` MinIO 部署中的 ``mybucket`` 存储桶启用版本控制：

      .. code-block:: shell
         :class: copyable

          mc version enable myminio/mybucket

   .. tab-item:: 语法

      该命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] version enable ALIAS                \
	                                 --exclude-folders    \
					 --excluded-prefixes

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   MinIO 部署的 :ref:`alias <alias>` 与要启用版本控制的存储桶完整路径。例如：

   .. code-block:: shell

      mc version enable myminio/mybucket

.. mc-cmd:: --exclude-folders
   :optional:

   在指定存储桶中，对所有文件夹（名称以 ``/`` 结尾的对象）禁用版本控制。

.. mc-cmd:: --excluded-prefixes
   :optional:

   对匹配前缀列表的对象禁用版本控制，最多支持 10 个前缀。
   前缀列表会匹配前缀或名称中包含指定字符串的所有对象，类似 ``prefix*`` 形式的正则表达式。
   如果仅按前缀匹配对象，请使用 ``prefix/*``。

   例如，以下命令将前缀或名称中包含 ``_test`` 或 ``_temp`` 的对象排除在版本控制之外：

   .. code-block:: shell
      :class: copyable

      mc version enable --excluded-prefixes "_test, _temp" myminio/mybucket


全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals


示例
----

启用存储桶版本控制
~~~~~~~~~~~~~~~~

使用 :mc:`mc version enable` 为存储桶启用版本控制：

.. code-block:: shell
   :class: copyable

   mc version enable ALIAS/PATH

- 将 :mc-cmd:`ALIAS <mc version enable ALIAS>` 替换为已配置 MinIO 部署的 :mc:`alias <mc alias>`。

- 将 :mc-cmd:`PATH <mc version enable ALIAS>` 替换为要启用版本控制的存储桶。


行为
----

现有数据的存储桶版本控制
~~~~~~~~~~~~~~~~~~~~~~

在包含现有数据的存储桶上启用版本控制后，会立即为每个未版本化对象创建值为 ``NULL`` 的版本 ID。


S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
