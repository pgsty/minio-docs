.. _minio-mc-admin-accesskey-disable:

==============================
``mc admin accesskey disable``
==============================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc admin accesskey disable


语法
----

.. start-mc-admin-accesskey-disable-desc

:mc-cmd:`mc admin accesskey disable` 命令用于禁用 MinIO IDP 用户的现有访问密钥。

.. end-mc-admin-accesskey-disable-desc

.. tab-set::

   .. tab-item:: 示例

      以下命令会禁用指定的访问密钥：
  
      .. code-block:: shell  
         :class: copyable 
  
         mc admin accesskey disable myminio myuserserviceaccount  

   .. tab-item:: 语法

      该命令的语法如下：
  
      .. code-block:: shell  
         :class: copyable 
  
         mc [GLOBALFLAGS] admin accesskey disable         \  
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

   要禁用的访问密钥。


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
