.. _minio-mc-admin-accesskey-list:

=========================
``mc admin accesskey ls``
=========================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc admin accesskey list
.. mc:: mc admin accesskey ls


语法
------

.. start-mc-admin-accesskey-list-desc

:mc:`mc admin accesskey ls` 命令列出 MinIO 部署管理的用户、访问密钥或临时 :ref:`security token service <minio-security-token-service>` 密钥。

.. end-mc-admin-accesskey-list-desc

:mc:`mc admin accesskey list` 别名与 :mc:`mc admin accesskey ls` 功能等效。

.. tab-set::

   .. tab-item:: 示例

      以下命令列出别名为 ``myminio`` 的部署中，用户名为 ``admin1`` 的用户关联的所有访问密钥：

      .. code-block:: shell  
         :class: copyable 

         mc admin accesskey ls myminio admin1

      输出如下所示：
   
      .. code-block:: shell

            Access Key        | Expiry
         5XF3ZHNZK6FBDWH9JMLX | 2023-06-24 07:00:00 +0000 UTC
         F4V2BBUZSWY7UG96ED70 | 2023-12-24 18:00:00 +0000 UTC
         FZVSEZ8NM9JRBEQZ7B8Q | no-expiry
         HOXGL8ON3RG0IKYCHCUD | no-expiry

	 
   .. tab-item:: 语法

      该命令使用以下语法： 
  
      .. code-block:: shell  
         :class: copyable 
  
         mc [GLOBALFLAGS] admin accesskey ls             \  
                                          ALIAS          \ 
                                          [USER]         \
                                          [--all]        \
                                          [--self]       \
                                          [--temp-only]  \
                                          [--users-only]

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


参数
~~~~~~~~~~

.. mc-cmd:: ALIAS
   :required:

   MinIO 部署的 :mc-cmd:`alias <mc alias>`。

.. mc-cmd:: USER
   :optional:

   要显示其访问密钥的用户名称。
   多个用户名之间使用空格分隔。

.. mc-cmd:: --all
   :optional:

   列出所有用户及其关联的任意访问密钥或临时 STS 密钥。
   需要具有该部署的管理员权限。

   此标志与该命令的其他可用标志互斥。

.. mc-cmd:: --svcacc-only
   :optional:

   列出部署上的临时 :ref:`Security Token Service (STS) keys <minio-security-token-service>`。

   此标志与该命令的其他可用标志互斥。

.. mc-cmd:: --self
   :optional:

   列出当前已认证用户的访问密钥和 STS 密钥。

   此标志与该命令的其他可用标志互斥。

.. mc-cmd:: --temp-only
   :optional:

   列出用户及其访问密钥。
   仅返回已关联访问密钥的用户。

   运行命令的用户必须具有管理员权限才能使用此标志。

   此标志与该命令的其他可用标志互斥。

.. mc-cmd:: --users-only
   :optional:

   列出该部署管理的 MinIO 用户。
   与 :mc-cmd:`~mc admin accesskey ls --all` 标志配合使用可列出部署上的所有用户。

全局标志
~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
--------

列出所有内置用户及其关联访问密钥
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下命令列出别名为 ``myminio`` 的 MinIO 部署所管理的所有用户，以及其关联的任意访问密钥或临时 STS 令牌。

.. code-block:: shell
   :class: copyable

   mc admin accesskey list myminio/ --all

返回当前已认证用户的访问密钥列表
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下命令列出 ``myminio`` 部署中与当前已认证用户关联的访问密钥或临时 STS 令牌。

.. code-block:: shell
   :class: copyable

   mc admin accesskey list myminio/ --self

列出由部署创建并管理的所有用户
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下命令返回当前部署上的所有用户列表。
该列表仅包含由 MinIO IDP 管理的用户，不包含通过 OpenID 或 Active Directory/LDAP 等协议由第三方工具管理的用户。

.. code-block:: shell
   :class: copyable

   mc admin accesskey ls myminio/ --all --users-only

返回与用户 ``miniouser1`` 和 ``miniouser2`` 关联的访问密钥列表
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下命令返回 ``myminio`` 部署上这两个用户的访问密钥列表。

.. code-block:: shell
   :class: copyable

   mc admin accesskey ls myminio/ miniouser1 miniouser2

行为
--------

S3 兼容性
~~~~~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
