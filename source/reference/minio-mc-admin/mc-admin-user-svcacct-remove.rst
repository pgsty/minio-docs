.. _minio-mc-admin-svcacct-remove:

============================
``mc admin user svcacct rm``
============================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc admin user svcacct remove
.. mc:: mc admin user svcacct rm

.. important::

   此命令已被替代，并将在未来的 MinIO 客户端版本中弃用。

   从 MinIO客户端版本RELEASE.2024-10-08T09-37-26Z 起，请使用 :mc:`mc admin accesskey rm` 命令删除内置 MinIO IDP 用户的访问密钥。

   对于 AD/LDAP 用户的访问密钥，请使用 :mc:`mc idp ldap accesskey rm` 命令。


语法
----

.. start-mc-admin-svcacct-remove-desc

:mc:`mc admin user svcacct rm` 命令会删除部署中与某个用户关联的访问密钥。

.. end-mc-admin-svcacct-remove-desc

:mc:`mc admin user svcacct remove` 命令与 :mc:`mc admin user svcacct rm` 的功能等效。
   
删除后，应用程序将无法再使用该访问密钥进行身份验证。
   
.. tab-set::

   .. tab-item:: 示例

      以下命令会删除指定的访问密钥：
  
      .. code-block:: shell  
         :class: copyable 
  
         mc admin user svcacct rm myminio myuserserviceaccount  

   .. tab-item:: 语法

      命令语法如下： 
  
      .. code-block:: shell  
         :class: copyable 
  
         mc [GLOBALFLAGS] admin user svcacct remove          \ 
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

   要删除的服务账户访问密钥。


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
