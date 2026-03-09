.. _minio-mc-admin-user-list:

====================
``mc admin user ls``
====================

.. default-domain:: minio

.. contents:: Table of Contents
   :local:
   :depth: 2

.. mc:: mc admin user list
.. mc:: mc admin user ls


语法
----

.. start-mc-admin-user-list-desc

:mc:`mc admin user ls` 命令会列出目标 MinIO 部署上的所有 :ref:`MinIO 用户 <minio-internal-idp>`。

.. end-mc-admin-user-list-desc

:mc:`mc admin user list` 命令与 :mc:`mc admin user ls` 功能等效。

:mc-cmd:`mc admin user ls` *不会* 返回与用户关联的 access key 或 secret key。
使用 :mc-cmd:`mc admin user info` 可获取用户详细信息，包括用户的 access key。

要管理外部 Identity Provider 用户，请参阅 :mc:`OIDC <mc idp openid>` 或 :mc:`AD/LDAP <mc idp ldap>`。

.. tab-set::

   .. tab-item:: 示例

      以下命令会列出 ``myminio`` MinIO 部署上的所有用户：

      .. code-block:: shell
         :class: copyable

         mc admin user ls myminio

   .. tab-item:: 语法

      该命令具有以下语法：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] admin user list   \
                                     ALIAS

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   已配置 MinIO 部署的 :mc-cmd:`alias <mc alias>`，命令将从该部署中列出用户。


全局参数
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals


示例
----

列出可用用户
~~~~~~~~~~~~

使用 :mc-cmd:`mc admin user ls` 列出 MinIO 部署上的所有用户：

.. code-block:: shell
   :class: copyable

   mc admin user ls ALIAS

- 将 :mc-cmd:`ALIAS <mc admin user ls ALIAS>` 替换为该 MinIO 部署的 :mc-cmd:`alias <mc alias>`。

输出类似如下：

.. code-block:: shell

   enabled    devadmin              readwrite
   enabled    devtest               readonly
   enabled    newuser


行为
----

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
