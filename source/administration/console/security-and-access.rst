

.. _minio-console-security-access:

===================
安全与访问
===================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

你可以使用 MinIO Console 执行 MinIO 提供的多种身份与访问管理功能，例如：

- 创建继承父级权限的子 :ref:`访问密钥 <minio-console-user-access-keys>`。
- 查看、管理和创建访问 :ref:`策略 <minio-console-admin-policies>`。
- 使用内置的 MinIO IDP 创建和管理 :ref:`用户凭证 <minio-console-admin-identity>` 或组，连接一个或多个 OIDC provider，或添加 AD/LDAP provider 以实现 SSO。


.. _minio-console-user-access-keys:

访问密钥
-----------

:guilabel:`Access Keys` 或 `Service Accounts` 部分会显示与已认证用户关联的所有 :ref:`minio-id-access-keys`。
某个用户已有访问密钥的摘要列表包括访问密钥、过期时间、状态、名称和描述。

访问密钥可为应用程序提供认证凭证，并继承“父”用户的权限。

对于使用外部身份管理器（例如 Active Directory 或兼容 OIDC 的 provider）的部署，访问密钥为用户提供了一种创建长期有效凭证的方式。

- 你可以选择访问密钥所在行查看其自定义策略（如果存在）。

   你可以在此页面创建或修改策略。
   访问密钥策略不能超过父用户被授予的权限。

- 你可以选择 :guilabel:`Create access key` 创建新的访问密钥。

   Console 会自动生成访问密钥和密码。
   你可以选择密码字段上的眼睛 :octicon:`eye` 图标以显示该值。
   你也可以根据需要覆盖这些值。

   你可以为访问密钥设置自定义策略，进一步限制使用该密钥进行认证的用户所拥有的权限。
   选择 :guilabel:`Restrict beyond user policy` 打开策略编辑器，并按需修改。

   在选择 :guilabel:`Create` 创建访问密钥之前，请确保你已将访问密钥密码保存到安全位置。
   创建访问密钥后，你无法再次获取或重置该密码值。

   如需轮换应用程序凭证，请创建新的访问密钥，并在应用程序切换为使用新凭证后删除旧密钥。

.. _minio-console-admin-policies:

策略
--------

:guilabel:`Policies` 部分会显示 MinIO 部署上的所有 :ref:`策略 <minio-policy>`。
`Policies` 部分允许你创建、修改或删除策略。

:ref:`策略 <minio-policy>` 定义了已认证用户可以访问的授权操作和资源。
每个策略描述用户、用户组或访问密钥可以执行的一项或多项操作，或其必须满足的条件。

这些策略是 JSON 格式的文本文件，并兼容 Amazon AWS Identity and Access Management 的策略语法、结构和行为。
有关在 MinIO 中使用策略管理访问的详细信息，请参阅 :ref:`Policy Based Action Control <minio-policy>`。

如果已认证用户没有 :ref:`所需的管理权限 <minio-policy-mc-admin-actions>`，则此部分或其中的内容可能不可见。

- 选择 :guilabel:`+ Create Policy` 创建新的 MinIO Policy。

- 选择策略所在行以管理该策略的详细信息。

  :guilabel:`Summary` 视图显示策略摘要。

  :guilabel:`Users` 视图显示所有分配了该策略的用户。

  :guilabel:`Groups` 视图显示所有分配了该策略的组。

  :guilabel:`Raw Policy` 视图显示原始 JSON 策略。

使用 :guilabel:`Users` 和 :guilabel:`Groups` 视图，可分别将已创建的策略分配给用户和组。

.. _minio-console-admin-identity:

身份
--------

:guilabel:`Identity` 部分为 :ref:`MinIO 管理的用户 <minio-users>` 提供管理界面。

该部分包含以下子部分。
如果已认证用户没有 :ref:`所需的管理权限 <minio-policy-mc-admin-actions>`，某些子部分可能不可见。

用户
~~~~~

:guilabel:`Users` 部分显示部署中的所有 MinIO 管理 :ref:`用户 <minio-users>`。

对于使用外部身份管理器（例如 Active Directory 或兼容 OIDC 的 provider）的部署，此部分不可见。

- 选择 :guilabel:`Create User` 创建新的 MinIO 管理用户。
        
  你可以在创建时为该用户分配 :ref:`组 <minio-groups>` 和 :ref:`策略 <minio-policy>`。

- 选择某个用户所在行以查看该用户的详细信息。
        
  你可以查看并修改该用户已分配的 :ref:`组 <minio-groups>` 和 :ref:`策略 <minio-policy>`。
        
  你还可以查看和管理与该用户关联的所有 :ref:`访问密钥 <minio-idp-service-account>`。

组
~~~~~~

:guilabel:`Groups` 部分显示 MinIO 部署上的所有 :ref:`组 <minio-groups>`。

对于使用外部身份管理器（例如 Active Directory 或兼容 OIDC 的 provider）的部署，此部分不可见。

- 选择 :guilabel:`Create Group` 创建新的 MinIO Group。
        
  你可以在创建时将新用户分配到该组。

  你可以在创建后为该组分配策略。

- 选择组所在行以打开该组的详细信息。

  你可以在 :guilabel:`Members` 视图中修改组成员。
        
  你可以在 :guilabel:`Policies` 视图中修改该组已分配的策略。

  更改用户的组成员关系会修改该用户继承的策略。更多信息请参阅 :ref:`minio-access-management`。

OpenID
~~~~~~

MinIO 支持使用 :ref:`兼容 OpenID Connect (OIDC) 的身份提供者 (IDP) <minio-external-identity-management-openid>` 对用户身份进行外部管理。

OpenID provider 的示例包括：

- Okta
- KeyCloak
- Dex
- Google
- Facebook 

配置外部 IDP 后即可启用 Single-Sign On 工作流，应用程序会先针对外部 IDP 完成认证，然后再访问 MinIO。

使用本节中的界面可以查看、添加或编辑部署的 OIDC 配置。
MinIO 支持任意数量的活动 OIDC 配置。

.. _minio-console-admin-identity-ldap:

LDAP
~~~~

MinIO 支持使用 :ref:`Active Directory 或 LDAP (AD/LDAP) <minio-external-identity-management-ad-ldap>` 服务对用户身份进行外部管理。
配置外部身份提供者 (IDP) 后即可启用 Single-Sign On (SSO) 工作流，应用程序会先针对外部 IDP 完成认证，然后再访问 MinIO。

使用本节中的界面可以查看、添加或编辑部署的 LDAP 配置。
MinIO 仅支持一个活动 LDAP 配置。

MinIO 会查询 Active Directory / LDAP 服务器，以验证客户端指定的凭证。
如果进行了相应配置，MinIO 还会在 AD/LDAP 服务器上执行组查找。
