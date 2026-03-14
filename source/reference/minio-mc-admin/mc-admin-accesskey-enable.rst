.. _minio-mc-admin-accesskey-enable:

=============================
``mc admin accesskey enable``
=============================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc admin accesskey enable


语法
----

.. start-mc-admin-accesskey-enable-desc

:mc-cmd:`mc admin accesskey enable` 命令用于启用现有访问密钥。

.. end-mc-admin-accesskey-enable-desc

.. tab-set::

   .. tab-item:: 示例

      以下命令启用指定的访问密钥：
  
      .. code-block:: shell  
         :class: copyable 
  
         mc admin accesskey enable myminio myuserserviceaccount  

   .. tab-item:: 语法

      该命令具有以下语法：
  
      .. code-block:: shell  
         :class: copyable 
  
         mc [GLOBALFLAGS] admin accesskey enable          \  
                                          ALIAS           \  
                                          SERVICEACCOUNT 

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   MinIO 部署的 :mc-cmd:`alias <mc alias>`。

.. mc-cmd:: SERVICEACCOUNT
   :required:

   要启用的访问密钥。


全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals


行为
----

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
