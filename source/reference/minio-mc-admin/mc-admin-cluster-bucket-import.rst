.. _minio-mc-admin-cluster-bucket-import:

==================================
``mc admin cluster bucket import``
==================================

.. default-domain:: minio

.. mc:: mc admin cluster bucket import

描述
----

.. versionadded:: RELEASE.2022-06-17T02-52-50Z

.. start-mc-admin-cluster-bucket-import-desc

:mc:`mc admin cluster bucket import` 命令用于导入由 :mc:`mc admin cluster bucket export` 命令生成的存储桶元数据。

.. end-mc-admin-cluster-bucket-import-desc

你可以使用此命令将元数据手动恢复到 MinIO 部署中的指定存储桶。

如果仅将部署指定为目标，此命令会将元数据对象应用到目标上所有匹配的存储桶。

.. tab-set::

   .. tab-item:: 示例

      以下命令将指定的元数据导入到 ``myminio`` 部署。
  
      .. code-block:: shell  
         :class: copyable 
  
         mc admin cluster bucket import myminio ~/minio-metadata-backup/myminio-cluster.zip

   .. tab-item:: 语法

      命令语法如下：
  
      .. code-block:: shell  
         :class: copyable 
  
         mc [GLOBALFLAGS] admin cluster bucket import  \
                                             ALIAS[/BUCKET] \
                                             METADATA.ZIP  
                                             
      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   MinIO 部署的 :mc-cmd:`alias <mc alias>`。

.. mc-cmd:: METADATA.ZIP
   :required:

   要导入的元数据文件路径。
   
   使用 :mc:`mc admin cluster bucket export` 导出存储桶元数据，以供此命令使用。

.. mc-cmd:: BUCKET
   :optional:

   应用导入元数据的存储桶。


全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals
