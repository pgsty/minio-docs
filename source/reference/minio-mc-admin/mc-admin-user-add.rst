.. _minio-mc-admin-user-add:

=====================
``mc admin user add``
=====================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc admin user add


语法
----

.. start-mc-admin-user-add-desc

:mc:`mc admin user add` 命令会在目标 MinIO 部署中添加一个新的 :ref:`MinIO 用户 <minio-internal-idp>`。

.. end-mc-admin-user-add-desc

要管理外部身份提供商用户，请参见 :mc:`OIDC <mc idp openid>` 或 :mc:`AD/LDAP <mc idp ldap>`。

.. tab-set::

   .. tab-item:: 示例

      以下命令会在 ``myminio`` MinIO 部署上创建一个新用户 ``newuser``：

      .. code-block:: shell
         :class: copyable

         mc admin user add myminio newuser newusersecret

   .. tab-item:: 语法

      该命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] admin user add        \
                                     ALIAS      \
                                     ACCESSKEY  \
                                     SECRETKEY

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


参数
~~~~

.. mc-cmd:: ACCESSKEY
   :required:

   用于唯一标识新用户的访问密钥，类似于用户名。

.. mc-cmd:: ALIAS
   :required:

   已配置 MinIO 部署的 :mc-cmd:`alias <mc alias>`，用于在该部署上创建新用户。

.. mc-cmd:: SECRETKEY
   :required:

   新用户的 secret key。创建 secret key 时请参考以下建议：
   
   - 密钥应为*唯一*值
   - 密钥应足够*长*（超过 12 个字符）
   - 密钥应具备*复杂性*（混合使用字符、数字和符号）


全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals


示例
----

创建新用户
~~~~~~~~~~

使用 :mc-cmd:`mc admin user add` 在 MinIO 部署上创建用户：

.. code-block:: shell
   :class: copyable

      mc admin user add ALIAS ACCESSKEY SECRETKEY

- 将 :mc-cmd:`ALIAS <mc admin user add ALIAS>` 替换为 MinIO 部署的 :mc-cmd:`alias <mc alias>`。

- 将 :mc-cmd:`ACCESSKEY <mc admin user add ACCESSKEY>` 替换为该用户的 access key。

- 将 :mc-cmd:`SECRETKEY <mc admin user add SECRETKEY>` 替换为该用户的 secret key。
  MinIO *不*提供任何在设置后检索 secret key 的方法。

请为 ``ACCESSKEY`` 和 ``SECRETKEY`` 都指定唯一、随机且足够长的字符串。
组织在生成用于 access key 或 secret key 的值时，可能存在特定的内部或监管要求。


行为
----

新用户默认没有策略
~~~~~~~~~~~~~~~~~~

新创建的用户默认*没有*任何策略，因此无法在 MinIO 部署上执行任何操作。
要配置用户的已分配策略，你可以执行以下一项或两项操作：

- 使用 :mc-cmd:`mc admin policy attach` 将一个或多个策略关联到用户。

- 使用 :mc-cmd:`mc admin group add` 将用户关联到组。
  用户会继承分配给该组的所有策略。

有关 MinIO 用户和组的更多信息，请参见 :ref:`minio-users` 和 :ref:`minio-groups`。
有关 MinIO 策略的更多信息，请参见 :ref:`MinIO Policy Based Access Control <minio-policy>`。

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
