.. _minio-mc-admin-svcacct-list:

============================
``mc admin user svcacct ls``
============================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc admin user svcacct list
.. mc:: mc admin user svcacct ls


.. important::

   此命令已被替代，并将在未来的 MinIO Client 版本中弃用。

   从 MinIO Client RELEASE.2024-10-08T09-37-26Z 起，请使用 :mc:`mc admin accesskey ls` 命令列出 MinIO 内置 IDP 用户的访问密钥。

   对于 AD/LDAP 用户的访问密钥，请使用 :mc:`mc idp ldap accesskey ls` 命令。

语法
----

.. start-mc-admin-svcacct-list-desc

:mc:`mc admin user svcacct ls` 命令列出与指定用户关联的所有访问密钥。

.. end-mc-admin-svcacct-list-desc

:mc:`mc admin user svcacct list` 别名与 :mc:`mc admin user svcacct ls` 具有等效功能。

.. tab-set::

   .. tab-item:: 示例

      以下命令列出用户名为 ``admin1`` 的用户关联的所有访问密钥：

      .. code-block:: shell  
         :class: copyable 

         mc admin user svcacct ls myminio admin1

      输出如下所示：
   
      .. code-block:: shell

            Access Key        | Expiry
         5XF3ZHNZK6FBDWH9JMLX | 2023-06-24 07:00:00 +0000 UTC
         F4V2BBUZSWY7UG96ED70 | 2023-12-24 18:00:00 +0000 UTC
         FZVSEZ8NM9JRBEQZ7B8Q | no-expiry
         HOXGL8ON3RG0IKYCHCUD | no-expiry

      .. versionadded:: RELEASE.2023-05-26T23-31-54Z

        访问密钥列表包含到期时间；对于永不过期的密钥，显示 ``no-expiry``。
	 
   .. tab-item:: 语法

      命令语法如下： 
  
      .. code-block:: shell  
         :class: copyable 
  
         mc [GLOBALFLAGS] admin user svcacct ls   \ 
                                             ALIAS  \ 
                                             USER

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   MinIO 部署的 :mc-cmd:`alias <mc alias>`。

.. mc-cmd:: USER
   :required:

   要列出其访问密钥的用户名。


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
