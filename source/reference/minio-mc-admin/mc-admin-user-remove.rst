.. _minio-mc-admin-user-remove:

=====================
``mc admin user rm``
=====================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc admin user remove
.. mc:: mc admin user rm

语法
----

.. start-mc-admin-user-remove-desc

:mc:`mc admin user rm` 命令用于在目标 MinIO 部署上移除 :ref:`MinIO 用户 <minio-internal-idp>`。

.. end-mc-admin-user-remove-desc

:mc:`mc admin user remove` 命令与 :mc:`mc admin user rm` 的功能等效。

要管理外部身份提供商用户，请参阅 :mc:`OIDC <mc idp openid>` 或 :mc:`AD/LDAP <mc idp ldap>`。

.. tab-set::

   .. tab-item:: 示例

      以下命令会在 ``myminio`` MinIO 部署上移除用户 ``myuser``：

      .. code-block:: shell
         :class: copyable

         mc admin user rm myminio myuser

   .. tab-item:: 语法

      在目标 MinIO 部署上移除用户。

      命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] admin user remove    \
                                     ALIAS     \
				     USERNAME

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   已配置 MinIO 部署的 :mc:`alias <mc alias>`，该部署中包含要移除的用户。

.. mc-cmd:: USERNAME
   :required:

   要移除的用户名。

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

移除用户
~~~~~~~~

使用 :mc-cmd:`mc admin user rm` 从 MinIO 部署中移除用户：

.. code-block:: shell
   :class: copyable

   mc admin user rm ALIAS USERNAME

- 将 :mc-cmd:`ALIAS <mc admin user rm ALIAS>` 替换为 MinIO 部署的 :mc-cmd:`alias <mc alias>`。

- 将 :mc-cmd:`USERNAME <mc admin user rm USERNAME>` 替换为要移除的用户名。

行为
----

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
