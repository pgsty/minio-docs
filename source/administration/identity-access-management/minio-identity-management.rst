.. _minio-internal-idp:

=================
MinIO 身份管理
=================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

MinIO 内置一个 IDentity Provider (IDP)，提供核心身份管理功能。
MinIO IDP 支持在部署中创建任意数量的长期有效用户，以支持客户端认证。

每个用户都由唯一的访问密钥（用户名）及其对应的密钥（密码）组成。
客户端必须同时提供现有 MinIO 用户的有效访问密钥（用户名）及其对应的密钥（密码），才能完成身份认证。

管理员使用 :mc:`mc admin user` 命令创建和管理 MinIO 用户。

MinIO 还支持创建 :ref:`访问密钥
<minio-idp-service-account>`。访问密钥是已认证父用户的子身份，并继承父用户的权限。

默认情况下，MinIO 会拒绝访问任何未被用户已分配或继承 :ref:`策略 <minio-policy>` 显式允许的操作或资源。
你必须显式为用户分配一个描述其授权操作和资源的 :ref:`策略 <minio-policy>`，*或者* 将用户加入已关联策略的 :ref:`组
<minio-groups>`。更多信息请参见 :ref:`minio-access-management`。

.. admonition:: 外部身份管理
   :class: note

   MinIO 支持使用 OpenID Connect (OIDC) 或 Active Directory/LDAP IDentity Provider (IDP) 对身份进行外部管理。
   更多信息请参见：

   - :ref:`minio-external-identity-management-openid`
   - :ref:`minio-external-identity-management-ad-ldap`

   AD/LDAP 与 OIDC 配置互斥。
   此外，启用 AD/LDAP 外部身份管理后，会禁用 MinIO 内部 IDP，但仍可创建 :ref:`访问密钥
   <minio-idp-service-account>`。
   你可以在保留 MinIO 管理用户的同时，配置多个 OIDC provider。

.. toctree::
   :titlesonly:
   :hidden:

   /administration/identity-access-management/minio-user-management.rst
   /administration/identity-access-management/minio-group-management.rst
