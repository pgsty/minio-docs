.. _minio-mc-admin-user-disable:

=========================
``mc admin user disable``
=========================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc admin user disable


语法
----

.. start-mc-admin-user-disable-desc

:mc:`mc admin user disable` 命令用于在目标 MinIO 部署上禁用 :ref:`MinIO 用户 <minio-internal-idp>`。

.. end-mc-admin-user-disable-desc

客户端不能使用该用户凭证向 MinIO 部署进行身份验证。
禁用用户 *不会* 将该用户从部署中移除。
使用 :mc-cmd:`mc admin user enable` 可在 MinIO 部署上启用已禁用的用户。

如需管理外部身份提供商用户，请参阅 :mc:`OIDC <mc idp openid>` 或 :mc:`AD/LDAP <mc idp ldap>`。

.. tab-set::

   .. tab-item:: 示例

      以下命令会在 ``myminio`` MinIO 部署上禁用用户 ``myuser``：

      .. code-block:: shell
         :class: copyable

         mc admin user disable myminio myuser

   .. tab-item:: 语法

      该命令的语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] admin user disable   \
                                     ALIAS     \
				     USERNAME

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   要禁用用户所在 MinIO 部署的 :mc:`alias <mc alias>`。

.. mc-cmd:: USERNAME
   :required:

   要禁用的用户名。


全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

禁用用户
~~~~~~~~

使用 :mc-cmd:`mc admin user disable` 在 MinIO 部署上禁用用户。

.. code-block:: shell
   :class: copyable

   mc admin user disable ALIAS USERNAME

- 将 :mc-cmd:`ALIAS <mc admin user disable ALIAS>` 替换为 MinIO 部署的 :mc-cmd:`alias <mc alias>`。

- 将 :mc-cmd:`USERNAME <mc admin user disable USERNAME>` 替换为要禁用的用户名。


行为
----

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
