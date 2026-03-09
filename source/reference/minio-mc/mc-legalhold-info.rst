.. _minio-mc-legalhold-info:

=====================
``mc legalhold info``
=====================

.. default-domain:: minio

.. contents:: Table of Contents
   :local:
   :depth: 2

.. mc:: mc legalhold info

.. |command| replace:: :mc:`mc legalhold info`
.. |rewind| replace:: :mc-cmd:`~mc legalhold info --rewind`
.. |versionid| replace:: :mc-cmd:`~mc legalhold info --version-id`
.. |alias| replace:: :mc-cmd:`~mc legalhold info ALIAS`

语法
------

.. start-mc-legalhold-info-desc

:mc:`mc legalhold info` 命令返回一个或多个对象的当前 :ref:`legal hold
<minio-object-locking-legalhold>` 设置。

.. end-mc-legalhold-info-desc

:mc:`mc legalhold` *要求* 指定的存储桶已启用对象锁定。
你**只能**在创建存储桶时启用对象锁定。有关创建启用对象锁定的存储桶，请参阅
:mc-cmd:`mc mb --with-lock` 文档。

.. tab-set::

   .. tab-item:: EXAMPLE

      以下命令检索 ``myminio`` MinIO 部署中 ``mydata`` 存储桶内对象的当前 legal hold 状态：

      .. code-block:: shell
         :class: copyable

         mc legalhold info --recursive myminio/mydata

   .. tab-item:: SYNTAX

      该命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] legalhold info  \
                          [--recursive]   \
                          [--rewind]      \
                          [--version-id]  \
                          ALIAS

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~~~~~~~

.. mc-cmd:: ALIAS
   :required:

   MinIO :ref:`alias <alias>` 以及要为其启用 legal hold 的对象路径（或多个对象路径）。例如：

   .. code-block:: shell
      
      mc legalhold info play/mybucket/myobjects/objects.txt

.. mc-cmd:: --recursive, r
   :optional:

   返回 :mc-cmd:`~mc legalhold info ALIAS` 中存储桶或存储桶前缀下全部对象的 legal hold 状态。

.. mc-cmd:: --rewind
   :optional:

   .. include:: /includes/facts-versioning.rst
      :start-after: start-rewind-desc
      :end-before: end-rewind-desc

.. mc-cmd:: --version-id, vid
   :optional:

   .. include:: /includes/facts-versioning.rst
      :start-after: start-version-id-desc
      :end-before: end-version-id-desc

全局参数
~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
--------

获取对象的 Legal Hold 状态
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc:`mc legalhold info` 获取对象的 legal hold 状态。
添加 :mc-cmd:`~mc legalhold info --recursive` 以返回存储桶内容的 legal hold 状态：

.. code-block:: shell
   :class: copyable

   mc legalhold clear [--recursive] ALIAS/PATH

- 将 :mc-cmd:`ALIAS <mc legalhold info ALIAS>` 替换为 S3 兼容主机的
  :ref:`alias <alias>`。

- 将 :mc-cmd:`PATH <mc legalhold info ALIAS>` 替换为 S3 兼容主机上的存储桶或对象路径。
  如果指定的是存储桶路径或存储桶前缀，请包含
  :mc-cmd:`~mc legalhold info --recursive` 选项。


行为
--------

S3 兼容性
~~~~~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
