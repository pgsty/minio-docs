.. _minio-mc-admin-user-enable:

========================
``mc admin user enable``
========================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc admin user enable


语法
----

.. start-mc-admin-user-enable-desc

:mc:`mc admin user enable` 命令用于在目标 MinIO 部署上启用 :ref:`MinIO 用户 <minio-internal-idp>`。

.. end-mc-admin-user-enable-desc

客户端只能使用已启用的用户向 MinIO 部署进行认证。
使用 :mc-cmd:`mc admin user add` 创建的用户默认处于启用状态。

要管理外部身份提供商用户，请参阅 :mc:`OIDC <mc idp openid>` 或 :mc:`AD/LDAP <mc idp ldap>`。

.. tab-set::

   .. tab-item:: 示例

      以下命令在 ``myminio`` MinIO 部署上启用用户 ``myuser``：

      .. code-block:: shell
         :class: copyable

         mc admin user enable myminio myuser

   .. tab-item:: 语法

      该命令具有以下语法：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] admin user enable    \
                                     ALIAS     \
	                             USERNAME

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   需要启用该用户的 MinIO 部署的 :mc:`alias <mc alias>`。

.. mc-cmd:: USERNAME
   :required:

   要启用的用户名。


全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals


示例
----

启用用户
~~~~~~~~

使用 :mc-cmd:`mc admin user enable` 在 MinIO 部署上启用用户。

.. code-block:: shell
   :class: copyable

   mc admin user enable ALIAS USERNAME

- 将 :mc-cmd:`ALIAS <mc admin user enable ALIAS>` 替换为 MinIO 部署的 :mc-cmd:`alias <mc alias>`。

- 将 :mc-cmd:`USERNAME <mc admin user enable USERNAME>` 替换为要启用的用户名。


行为
----

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
