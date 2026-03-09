.. _minio-mc-admin-cluster-bucket-export:

==================================
``mc admin cluster bucket export``
==================================

.. default-domain:: minio

.. mc:: mc admin cluster bucket export

描述
----

.. versionadded:: RELEASE.2022-06-17T02-52-50Z

.. start-mc-admin-cluster-bucket-export-desc

:mc:`mc admin cluster bucket export` 命令会导出存储桶元数据，以供 :mc:`mc admin cluster bucket import` 命令使用。

.. end-mc-admin-cluster-bucket-export-desc

你可以使用此命令手动备份指定 MinIO 存储桶的元数据。
该命令始终将输出保存为 ``cluster-metadata.zip``。

如果你仅将部署指定为目标，此命令会备份目标部署上所有存储桶的元数据。

.. tab-set::

   .. tab-item:: 示例

      以下命令会导出 ``myminio`` 部署的所有存储桶元数据。
  
      .. code-block:: shell  
         :class: copyable 
  
         mc admin cluster bucket export myminio

   .. tab-item:: 语法

      该命令使用以下语法：
  
      .. code-block:: shell  
         :class: copyable 
  
         mc [GLOBALFLAGS] admin cluster bucket export  \
                                               ALIAS[/BUCKET]
                                             
      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   MinIO 部署的 :mc-cmd:`alias <mc alias>`。

.. mc-cmd:: BUCKET
   :optional:

   要导出元数据的存储桶。


全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals
