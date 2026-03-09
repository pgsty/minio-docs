.. _minio-mc-ilm-restore:

==================
``mc ilm restore``
==================

.. default-domain:: minio

.. contents:: Table of Contents
   :local:
   :depth: 2

.. mc:: mc ilm restore

语法
------

.. start-mc-ilm-restore-desc

:mc:`mc ilm restore` 命令会为归档在远程层上的对象创建一个临时副本。
默认情况下，该副本会在 1 天后自动过期。

.. end-mc-ilm-restore-desc

使用此命令可让应用程序通过 MinIO 部署访问分层对象（例如“热层”）。
归档对象会保留在远程层，而临时副本会成为该对象的 ``HEAD``。

.. versionadded:: mc RELEASE.2023-04-12T02-21-51Z

   使用 :mc:`mc stat` 可显示已恢复对象是从本地临时副本读取还是从远程层读取。
   当前正在从远程层恢复的对象会显示状态 ``Ongoing : true``。

.. tab-set::

   .. tab-item:: 示例

      以下命令将远程层上一个已转储对象的副本恢复到 ``myminio`` MinIO 部署：

      .. code-block:: shell
         :class: copyable

         mc ilm restore myminio/mybucket/object.txt

   .. tab-item:: 语法

      该命令的语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] ilm restore         \
                          [--days "int" ]     \
                          [--recursive]       \
                          [--vid "string"]    \
                          [--versions]        \
                          [--enc-c "string"]  \
                          ALIAS

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


参数
~~~~~~~~~~

.. mc-cmd:: ALIAS
   :required:

   要恢复的归档对象对应的 MinIO :ref:`alias <alias>`、存储桶和路径。

   .. code-block:: shell

      mc ilm restore myminio/mybucket/object.txt

.. mc-cmd:: --days                     
   :optional:

   MinIO 使已恢复归档对象副本过期前的天数。

.. block include of enc-c

.. include:: /includes/common-minio-sse.rst
   :start-after: start-minio-mc-sse-c-only
   :end-before: end-minio-mc-sse-options

.. mc-cmd:: --recursive, r                  
   :optional:

   恢复指定前缀下的所有对象。

.. mc-cmd:: --versions                       
   :optional:

   恢复远程层上该对象的所有版本。

.. mc-cmd:: --version-id, vid  
   :optional:

   恢复远程层上该对象的指定版本。

全局参数
~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
--------

恢复归档对象
~~~~~~~~~~~~~~~~~~~~~~~~~~

以下命令恢复一个已归档到远程层的对象：

.. code-block:: shell
   :class: copyable

   mc ilm restore myminio/mybucket/object.txt


恢复指定版本的归档对象
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下命令恢复一个已归档到远程层的指定对象版本：

.. code-block:: shell
   :class: copyable

   mc ilm restore --vid "VERSIONID" myminio/mybucket/object.txt

恢复存储桶前缀下的所有归档对象
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下命令恢复远程层上指定前缀下归档的所有对象：

.. code-block:: shell
   :class: copyable

   mc ilm restore --recursive myminio/mybucket/data/

行为
--------

已恢复对象自动过期
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

MinIO 会在指定天数后自动使已恢复对象副本过期（默认：1 天）。

已恢复对象会成为 HEAD
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

无论该对象命名空间的版本控制历史如何，已恢复的对象副本都会成为 HEAD。
在本地副本存在期间，这可能导致应用程序返回“过时”数据。 

S3 兼容性
~~~~~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
