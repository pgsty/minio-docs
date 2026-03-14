.. _minio-mc-admin-svcacct-add:

=============================
``mc admin user svcacct add``
=============================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc admin user svcacct add

.. important::

   此命令已被替代，并将在未来的 MinIO 客户端版本中弃用。

   从 MinIO客户端版本RELEASE.2024-10-08T09-37-26Z 起，请使用 :mc:`mc admin accesskey create` 命令为内置 MinIO IDP 用户添加访问密钥。

   如需为 AD/LDAP 用户添加访问密钥，请使用 :mc:`mc idp ldap accesskey create` 命令。


语法
----

.. start-mc-admin-svcacct-add-desc

:mc-cmd:`mc admin user svcacct add` 命令为现有 MinIO 或 AD/LDAP 用户添加新的访问密钥。

.. end-mc-admin-svcacct-add-desc

.. admonition:: OpenID Connect 用户的访问密钥
   :class: note

   要为 :ref:`OpenID Connect 用户 <minio-external-identity-management-openid>` 生成服务账户访问密钥，请使用 :ref:`MinIO Console <minio-console>`。

.. tab-set::

   .. tab-item:: 示例

      以下命令会创建一个与现有 MinIO 用户关联的新访问密钥：

      .. code-block:: shell                                                                 
         :class: copyable

         mc admin user svcacct add                       \
            --access-key "myuserserviceaccount"          \                                  
            --secret-key "myuserserviceaccountpassword"  \
            --policy "/path/to/policy.json"              \
            myminio myuser                                                                  
                                                                                   
      命令会返回该新账号的访问密钥和秘密密钥。

   .. tab-item:: 语法

      命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] admin user svcacct add             \
                                             [--access-key]  \
                                             [--secret-key]  \
                                             [--policy]      \
                                             [--comment]     \
                                             ALIAS           \
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

   MinIO 要为其添加新访问密钥的用户名。

   - 对于 :ref:`MinIO 管理的用户 <minio-users>`，指定该用户的访问密钥。
   - 对于 :ref:`Active Directory/LDAP 用户 <minio-external-identity-management-ad-ldap>`，指定用户的 Distinguished Name。
   - 对于 :ref:`OpenID Connect 用户 <minio-external-identity-management-openid>`，请使用 :ref:`MinIO Console <minio-console>` 生成访问密钥。

.. mc-cmd:: --access-key
   :optional:

   用作此账号访问密钥的字符串。
   省略时，MinIO 会自动生成一个 20 个字符的随机值。

   Access Key 名称在所有用户之间 *必须* 唯一。

.. mc-cmd:: --comment
   :optional:

   .. versionchanged:: RELEASE.2023-05-18T16-59-00Z
      已由 :mc-cmd:`~mc admin user svcacct add --description` 和 :mc-cmd:`~mc admin user svcacct add --name` 替代。
      
      最初在 RELEASE.2023-01-28T20-29-38Z 版本中引入。


   此选项已移除。
   请改用 ``--description`` 或 ``--name``。

.. mc-cmd:: --description
   :optional:

   .. versionadded:: RELEASE.2023-05-18T16-59-00Z

   为服务账户添加描述。
   例如，可以说明该服务账户存在的原因。

.. mc-cmd:: --expiry
   :optional:

   .. versionadded:: RELEASE.2023-05-30T22-41-38Z

   为服务账户设置过期日期。
   该日期必须是未来时间，不能设置已经过去的过期日期。

   允许的日期和时间格式：

   - ``2023-06-24``
   - ``2023-06-24T10:00``
   - ``2023-06-24T10:00:00``
   - ``2023-06-24T10:00:00Z``
   - ``2023-06-24T10:00:00-07:00``

.. mc-cmd:: --name
   :optional:

   .. versionadded:: RELEASE.2023-05-18T16-59-00Z

   为服务账户添加一个人类可读的名称。

.. mc-cmd:: --policy
   :optional:

   要附加到新访问密钥的 :ref:`策略文档 <minio-policy>` 路径，最大大小为 2048 个字符。
   附加的策略不能授予父用户策略未明确允许的任何操作或资源访问权限。
   

.. mc-cmd:: --secret-key
   :optional:

   与新账号关联的秘密密钥。
   省略时，MinIO 会自动生成一个 40 字符的随机值。


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
