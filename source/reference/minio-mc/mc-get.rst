==========
``mc get``
==========

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc get

.. versionadded:: mc RELEASE.2024-02-24T01-33-20Z

语法
----

.. start-mc-get-desc

:mc:`mc get` 命令将对象从目标 S3 部署下载到本地文件系统。

.. end-mc-get-desc

与 :mc:`mc cp` 或 :mc:`mc mirror` 相比，``mc get`` 为下载文件提供了更简化的接口。
``mc get`` 使用单向下载功能，以牺牲效率为代价，换取相较其他命令更低的功能复杂度。

.. tab-set::

   .. tab-item:: 示例

      以下命令将文件 ``logo.png`` 从 s3 源下载到本地文件系统路径 ``~/images/collateral/``。

      .. code-block:: shell
         :class: copyable

         mc get minio/marketing/logo.png ~/images/collateral


   .. tab-item:: 语法

      命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] get                      \
                          SOURCE                   \
                          TARGET                   \
                          [--enc-c string]         \
                          [--version-id, --vid value]

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~

.. mc-cmd:: SOURCE
   :required:

   要下载对象的完整路径，包含 :ref:`alias <minio-mc-alias>`、存储桶、prefix（如使用）和对象名。

.. mc-cmd:: TARGET
   :required:

   本地文件系统上的目标路径，命令会将下载的文件放置到该路径。

.. block include of enc-c

.. include:: /includes/common-minio-sse.rst
   :start-after: start-minio-mc-sse-c-only
   :end-before: end-minio-mc-sse-options

.. mc-cmd:: --version-id, --vid
   :optional:
   
   检索对象的特定版本。
   传入要检索对象的版本 ID。

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

从 MinIO 检索对象到本地文件系统
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下命令从别名 ``myminio`` 下的存储桶 ``mybucket`` 检索文件 ``myobject.csv``，并将其放置到本地文件系统路径 ``/my/local/folder``。

.. code-block:: shell
   :class: copyable

   mc get myminio/mybucket/myobject.csv /my/local/folder 

从 MinIO 检索加密对象
~~~~~~~~~~~~~~~~~~~~~

以下命令检索一个加密文件，并将其放置到本地文件夹路径。

.. code-block:: shell
   :class: copyable

   mc get --enc-c "play/mybucket/object=MDEyMzQ1Njc4OTAxMjM0NTY3ODkwMTIzNDU2Nzg5MDA" play/mybucket/object path-to/object 
