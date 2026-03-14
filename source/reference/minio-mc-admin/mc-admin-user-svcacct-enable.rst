.. _minio-mc-admin-svcacct-enable:

================================
``mc admin user svcacct enable``
================================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc admin user svcacct enable

.. important::

   此命令已被替代，并将在未来的 MinIO 客户端 发布版本中弃用。

   从 MinIO客户端版本RELEASE.2024-10-08T09-37-26Z 起，使用 :mc:`mc admin accesskey enable` 命令为内置 MinIO IDP 用户启用访问密钥。

   如需为 AD/LDAP 用户启用访问密钥，请使用 :mc:`mc idp ldap accesskey enable` 命令。


语法
----

.. start-mc-admin-svcacct-enable-desc

:mc-cmd:`mc admin user svcacct enable` 命令用于启用现有访问密钥。

.. end-mc-admin-svcacct-enable-desc

.. tab-set::

   .. tab-item:: 示例

      以下命令启用指定的访问密钥：
  
      .. code-block:: shell  
         :class: copyable 
  
         mc admin user svcacct enable myminio myuserserviceaccount  

   .. tab-item:: 语法

      该命令的语法如下： 
  
      .. code-block:: shell  
         :class: copyable 
  
         mc [GLOBALFLAGS] admin user svcacct enable          \  
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

   要启用的服务账户访问密钥。


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
