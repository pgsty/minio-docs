.. _minio-users:

===============
用户管理
===============

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

概述
--------

MinIO 用户由唯一的访问密钥（用户名）及其对应的密钥（密码）组成。
客户端必须同时指定现有 MinIO 用户的有效访问密钥（用户名）及其对应的密钥（密码），才能完成身份认证。

每个用户都可以分配一个或多个 :ref:`策略 <minio-policy>`，
这些策略会显式列出该用户有权访问的操作和资源。
用户还可以从其所属的 :ref:`组 <minio-groups>` 继承策略。

默认情况下，MinIO 会拒绝访问任何未被用户已分配或继承的 :ref:`策略 <minio-policy>` 显式允许的操作或资源。
你必须显式为用户分配一个描述其授权操作和资源的 :ref:`策略 <minio-policy>`，*或者* 将用户加入已关联策略的 :ref:`组
<minio-groups>`。更多信息请参见 :ref:`minio-access-management`。

本页介绍 MinIO 内部 IDentity Provider (IDP) 的用户管理。
MinIO 还支持使用 OpenID Connect (OIDC) 或 Active Directory/LDAP IDentity Provider (IDP) 对身份进行外部管理。
更多信息请参见：

- :ref:`minio-external-identity-management-openid`
- :ref:`minio-external-identity-management-ad-ldap`

启用外部身份管理后，会禁用 MinIO 内部 IDP，但仍可创建 :ref:`访问密钥
<minio-idp-service-account>`。

.. _minio-idp-service-account:
.. _minio-id-access-keys:

访问密钥
-----------

MinIO Access Keys（原称 "Service Accounts"）是已认证 MinIO 用户的子身份，其中也包括 :ref:`外部管理的身份 <minio-authentication-and-identity-management>`。
每个访问密钥都会基于其父用户附加的 :ref:`策略 <minio-policy>`，*或者* 父用户所属组的策略来继承权限。
访问密钥还支持可选的内联策略，用于进一步将访问限制为父用户可用操作和资源的子集。

MinIO 用户可以生成任意数量的访问密钥。
这使应用所有者可以为其应用生成访问密钥，而无需 MinIO 管理员介入。
由于生成的访问密钥拥有与父用户相同或更少的权限，管理员可以专注于管理顶层父用户，而无需细致管理这些生成的访问密钥。

你可以使用 :mc:`mc admin user svcacct add` 命令创建访问密钥。
通过这些方式创建的身份在你移除访问密钥或父账户之前不会过期。

你还可以通过 ``AssumeRole`` STS API 端点，以编程方式创建 :ref:`security token service <minio-security-token-service>` 账户。
STS token 默认在 1 小时后过期，但你可以将过期时间设置为自创建起最长 7 天。

.. admonition:: 访问密钥用于编程访问
   :class: dropdown, note

   访问密钥支持应用程序进行编程访问。
   你不能使用访问密钥登录 MinIO Console。

.. _minio-users-root:

MinIO ``root`` 用户
-------------------

每个 MinIO 部署都具有一个 ``root`` 用户，可访问该部署上的所有操作和资源，
无论配置的是哪种 :ref:`身份管理器
<minio-authentication-and-identity-management>`。当 :mc:`minio` 服务器首次启动时，
会通过检查以下环境变量的值来设置 ``root`` 用户凭证：

- :envvar:`MINIO_ROOT_USER`
- :envvar:`MINIO_ROOT_PASSWORD`

轮换 root 用户凭证时，需要为部署中的所有 MinIO 服务器更新其中一个或两个变量。
root 凭证应指定为*长、唯一且随机*的字符串。
存储访问密钥和密钥时应采取一切可能的防护措施，确保只有已知且受信任、并且*确实需要*该部署超级用户访问权限的人员才能获取 ``root`` 凭证。

- MinIO *强烈不建议* 在任何环境中（开发、预发或生产）使用 ``root`` 用户进行常规客户端访问。

- MinIO *强烈建议* 创建用户时，使每个客户端仅拥有执行其分配工作负载所需的最小操作和资源集合。

如果这些变量未设置，:mc:`minio` 默认分别使用 ``minioadmin`` 和
``minioadmin`` 作为访问密钥和密钥。无论部署环境如何，MinIO 都 *强烈不建议* 使用默认凭证。

.. admonition:: 旧版 Root 用户环境变量已弃用
   :class: dropdown, important

   MinIO :minio-release:`RELEASE.2021-04-22T15-44-28Z` 及后续版本已弃用以下用于设置或更新 root 用户凭证的变量：

   - :envvar:`MINIO_ACCESS_KEY` 表示新的访问密钥。
   - :envvar:`MINIO_SECRET_KEY` 表示新的密钥。
   - :envvar:`MINIO_ACCESS_KEY_OLD` 表示旧的访问密钥。
   - :envvar:`MINIO_SECRET_KEY_OLD` 表示旧的密钥。

用户管理
---------------

创建用户
~~~~~~~~~~~~~

使用 :mc:`mc admin user add` 命令在 MinIO 部署上创建新用户：

.. code-block:: shell
   :class: copyable

   mc admin user add ALIAS ACCESSKEY SECRETKEY

- 将 :mc-cmd:`ALIAS <mc admin user add ALIAS>` 替换为 MinIO 部署的
  :mc:`alias <mc alias>`。

- 将 :mc-cmd:`ACCESSKEY <mc admin user add ACCESSKEY>` 替换为用户的访问密钥。
  MinIO 允许在用户创建后，通过 :mc:`mc admin user info` 命令检索访问密钥。

- 将 :mc-cmd:`SECRETKEY <mc admin user add SECRETKEY>` 替换为用户的密钥。
  MinIO *不提供* 在密钥设置后再进行检索的任何方法。

``ACCESSKEY`` 和 ``SECRETKEY`` 都应指定为唯一、随机且足够长的字符串。
你的组织可能对生成用于访问密钥或密钥的值有特定的内部或监管要求。

创建用户后，使用 :mc:`mc admin policy attach` 为新用户关联
:ref:`MinIO 基于策略的访问控制 <minio-policy>`。
以下命令分配内置的 :userpolicy:`readwrite` 策略：

.. code-block:: shell
   :class: copyable

   mc admin policy attach ALIAS readwrite --user=USERNAME

将 ``USERNAME`` 替换为上一步创建的 ``ACCESSKEY``。

删除用户
~~~~~~~~~~~~~

使用 :mc:`mc admin user rm` 命令从 MinIO 部署中移除用户：

.. code-block:: shell
   :class: copyable

   mc admin user rm ALIAS USERNAME

- 将 :mc-cmd:`ALIAS <mc admin user rm ALIAS>` 替换为 MinIO 部署的
  :mc:`alias <mc alias>`。

- 将 :mc-cmd:`USERNAME <mc admin user rm USERNAME>` 替换为要移除的用户名。
