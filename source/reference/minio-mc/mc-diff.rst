.. _minio-mc-diff:

===========
``mc diff``
===========

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc diff

语法
----

.. start-mc-diff-desc

:mc:`mc diff` 命令用于计算两个文件系统目录或 MinIO 存储桶之间的差异。
:mc:`mc diff` 仅列出缺失的对象或大小不同的对象。:mc:`mc diff`
**不会**比较对象内容。

.. end-mc-diff-desc

.. tab-set::

   .. tab-item:: 示例

      以下命令用于比较本地文件系统中的一个对象与 ``myminio`` MinIO
      部署中 ``mydata`` 存储桶内一个对象之间的差异：

      .. code-block:: shell
         :class: copyable

         mc diff ~/mydata/myobject.txt myminio/mydata/myobject.txt

   .. tab-item:: 语法

      :mc:`mc diff` 命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] diff SOURCE TARGET

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~

.. mc-cmd:: SOURCE

   *必需* 要与 ``TARGET`` 比较的对象。

   对于来自 MinIO 的对象，
   指定 :mc:`alias <mc alias>` 以及该对象的完整路径
   （例如存储桶和对象路径）。例如：

   .. code-block:: none

      mc diff play/mybucket/object.txt ~/mydata/object.txt


   对于来自本地文件系统的对象，指定该对象的完整路径。例如：

   .. code-block:: none

      mc diff ~/mydata/object.txt play/mybucket/object.txt

.. mc-cmd:: TARGET

   *必需* 要与 ``SOURCE`` 比较的对象。

   对于来自 MinIO 的对象，
   指定 :mc:`alias <mc alias>` 以及该对象的完整路径
   （例如存储桶和对象路径）。例如：

   .. code-block:: none

      mc diff play/mybucket/object.txt ~/mydata/object.txt


   对于来自本地文件系统的对象，指定该对象的完整路径。例如：

   .. code-block:: none

      mc diff ~/mydata/object.txt play/mybucket/object.txt

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

.. include:: /includes/play-alias-available.rst
   :start-after: play-alias-only
   :end-before: end-play-alias-only

.. code-block:: shell
   :class: copyable

   mc diff play/bucket1 play/bucket2

行为
----

输出图例
~~~~~~~~

:mc:`mc diff` 在格式化 diff 输出时使用以下图例：

.. code-block:: none
   
   FIRST < SECOND - 对象仅存在于 FIRST
   FIRST > SECOND - 对象仅存在于 SECOND
   FIRST ! SECOND - FIRST 中存在较新的对象

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
