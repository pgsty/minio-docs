.. _minio-mc-admin-user-info:

======================
``mc admin user info``
======================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc admin user info

语法
----

.. start-mc-admin-user-info-desc

:mc:`mc admin user info` 命令返回目标 MinIO 部署中某个 :ref:`MinIO 用户 <minio-internal-idp>`的详细信息。

.. end-mc-admin-user-info-desc

如需管理外部身份提供商用户，请参见 :mc:`OIDC <mc idp openid>` 或 :mc:`AD/LDAP <mc idp ldap>`。

.. tab-set::

   .. tab-item:: EXAMPLE

      以下命令返回 ``myminio`` MinIO 部署中用户 ``myuser`` 的详细信息：

      .. code-block:: shell
         :class: copyable

         mc admin user info myminio myuser

   .. tab-item:: SYNTAX

      该命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] admin user info      \
                                     ALIAS     \
	                             USERNAME

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   用于获取用户信息的已配置 MinIO 部署的 :mc-cmd:`alias <mc alias>`。

.. mc-cmd:: USERNAME

   要查询信息的用户名。


全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

.. versionchanged:: RELEASE.2023-05-26T23-31-54Z

   ``mc admin user info --json`` 输出包含用户通过组成员关系继承的策略，位于 ``memberOf`` 中。

示例
----

查看用户详细信息
~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc admin user info` 查看 MinIO 部署中某个用户的详细信息：

.. code-block:: shell
   :class: copyable

   mc admin user info ALIAS USERNAME

- 将 :mc-cmd:`ALIAS <mc admin user info ALIAS>` 替换为 MinIO 部署的 :mc-cmd:`alias <mc alias>`。

- 将 :mc-cmd:`USERNAME <mc admin user info USERNAME>` 替换为要显示信息的用户名。

对于 :ref:`MinIO 内部身份提供商 (IDP) <minio-internal-idp>`，输出类似如下：

.. code-block:: shell

   AccessKey: miniouser
   Status: enabled
   PolicyName: 
   MemberOf: []
   Authentication: builtin (miniouser)

对于 LDAP 等 :ref:`第三方 <minio-external-identity-management>`身份服务，输出类似如下：

.. code-block:: shell

   AccessKey: uid=dillon,ou=people,ou=swengg,dc=min,dc=io
   Status: 
   PolicyName: consoleAdmin
   MemberOf: []
   Authentication: ldap/localhost:1389 (uid=dillon,ou=people,ou=swengg,dc=min,dc=io)

查看来自组成员关系的策略
~~~~~~~~~~~~~~~~~~~~~~~~

将 :mc-cmd:`mc admin user info` 与 :option::`--json <mc.--json>` 配合使用，可查看用户从其 :ref:`组成员关系 <minio-groups>`继承的策略：

.. code-block:: shell
   :class: copyable

   mc admin user info ALIAS USERNAME --json

- 将 :mc-cmd:`ALIAS <mc admin user info ALIAS>` 替换为 MinIO 部署的 :mc-cmd:`alias <mc alias>`。

- 将 :mc-cmd:`USERNAME <mc admin user info USERNAME>` 替换为要显示信息的用户名。

输出中的 ``memberOf`` 属性包含该用户所属组的列表，以及附加到各组的策略。
输出类似如下：

.. code-block:: shell

   {
    "status": "success",
    "accessKey": "myuser",
    "userStatus": "enabled",
    "memberOf": [
     {
      "name": "testingGroup",
      "policies": [
       "testingGroupPolicy"
      ]
    "authentication": builtin (myuser)
     }
    ]
   }


行为
----

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
