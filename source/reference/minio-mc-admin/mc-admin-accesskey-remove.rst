.. _minio-mc-admin-accesskey-remove:

=========================
``mc admin accesskey rm``
=========================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc admin accesskey remove
.. mc:: mc admin accesskey rm


语法
----

.. start-mc-admin-accesskey-remove-desc

:mc:`mc admin accesskey rm` 命令用于删除部署中与某个用户关联的访问密钥。

.. end-mc-admin-accesskey-remove-desc

:mc:`mc admin accesskey remove` 命令与 :mc:`mc admin accesskey rm` 的功能等效。
   
.. warning::

   删除后，应用程序将无法再使用该访问密钥进行身份验证。
   
.. tab-set::

   .. tab-item:: 示例

      以下命令会删除指定的访问密钥：
  
      .. code-block:: shell  
         :class: copyable 
  
         mc admin accesskey rm myminio myuserserviceaccount  

   .. tab-item:: 语法

      命令语法如下：
  
      .. code-block:: shell  
         :class: copyable 
  
         mc [GLOBALFLAGS] admin accesskey rm                \ 
                                          ALIAS             \ 
                                          ACCESSKEYTOREMOVE

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   MinIO 部署的 :mc-cmd:`alias <mc alias>`。

.. mc-cmd:: ACCESSKEYTOREMOVE
   :required:

   要删除的访问密钥。


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
