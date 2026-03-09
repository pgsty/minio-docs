.. _minio-external-identity-management-ad-ldap:

=========================================
Active Directory / LDAP 访问管理
=========================================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

MinIO 支持配置单个 Active Directory 或 LDAP (AD/LDAP) 服务，用于对用户身份进行外部管理。
启用 AD/LDAP 外部身份管理会禁用 :ref:`MinIO 内部 IDP <minio-internal-idp>`。

对于由外部 AD/LDAP 提供方管理的身份，MinIO 会使用用户的 Distinguished Name，并尝试将其映射到现有的 :ref:`策略 <minio-policy>`。

如果 AD/LDAP 配置中包含查询用户 AD/LDAP 组成员关系所需的设置，MinIO *还会* 使用这些组的 Distinguished Name，并尝试将每一个映射到现有的 :ref:`策略 <minio-policy>`。

默认情况下，MinIO 会拒绝访问所有未被用户已分配或继承的 :ref:`策略 <minio-policy>` 明确允许的操作或资源。
由 AD/LDAP 提供方管理的用户，必须在用户配置文件数据中指定所需策略。
如果没有任何策略与用户 DN 或组 DN 匹配，MinIO 会阻止该部署上对所有操作和资源的访问。

MinIO 用于认证用户并获取其组成员关系的具体 AD/LDAP 查询，是在 :ref:`使用 Active Directory / LDAP 身份管理部署集群 <minio-external-iam-ad-ldap>` 时配置的。
本页介绍如何创建与可能返回的 Distinguished Name 相匹配的 MinIO 策略。

认证与授权流程
----------------

使用 Active Directory / LDAP
凭证的应用程序登录流程如下：

1. 将 AD/LDAP 凭证提供给 MinIO Security Token Service (STS)
   :ref:`minio-sts-assumerolewithldapidentity` API 端点。

2. MinIO 针对 AD/LDAP 服务器校验所提供的凭证。

3. MinIO 检查是否存在名称与用户 Distinguished Name (DN) 匹配的
   :ref:`策略 <minio-policy>`，并将该策略分配给已认证用户。

   如果配置为执行组查询，MinIO 还会查询该用户所属的 AD/LDAP 组列表。
   MinIO 会检查是否存在名称与返回的组 DN 匹配的策略，并将该策略分配给
   已认证用户。
   
4. MinIO 会在 STS API 响应中返回临时凭证，其形式为 access key、
   secret key 和 session token。这些凭证的权限与名称匹配已认证用户
   DN *或* 某个组 DN 的那些策略一致。

MinIO 提供了一个示例 Go 应用程序
:minio-git:`ldap.go <minio/blob/master/docs/sts/ldap.go>`，用于处理完整的
登录流程。

AD/LDAP 用户也可以创建与其 AD/LDAP 用户 Distinguished Name 关联的
:ref:`访问密钥 <minio-idp-service-account>`。
访问密钥是长期有效的凭证，会继承父用户的权限。
父用户在创建访问密钥时还可以进一步限制这些权限。
使用以下方法之一创建新的访问密钥：

使用 :mc:`mc admin user svcacct add` 命令创建访问密钥。
将用户 Distinguished Name 指定为要关联访问密钥的用户名。


将策略映射到用户 DN
-------------------

以下命令使用 :mc:`mc idp ldap policy attach` 将现有 MinIO :ref:`策略 <minio-policy>` 关联到 AD/LDAP 用户 DN。

.. code-block:: shell

   mc idp ldap policy attach myminio consoleAdmin \ 
     --user='cn=sisko,cn=users,dc=example,dc=com'
   
   mc idp ldap policy attach myminio readwrite,diagnostics \
     --user='cn=dax,cn=users,dc=example,dc=com'

- MinIO 会将 :userpolicy:`consoleAdmin`
  策略分配给 DN 匹配 ``cn=sisko,cn=users,dc=example,dc=com`` 的已认证用户，
  从而授予其对 MinIO 服务器的完全访问权限。

- MinIO 会将 :userpolicy:`readwrite` 和
  :userpolicy:`diagnostics` 两个策略分配给 DN 匹配
  ``cn=dax,cn=users,dc=example,dc=com`` 的已认证用户，从而授予其对
  MinIO 服务器的一般读写访问权限，*以及* 诊断性管理操作的访问权限。

- MinIO 不会为 DN 匹配 ``cn=quark,cn=users,dc=example,dc=com`` 的
  已认证用户分配任何策略，并会拒绝其对所有 API 操作的访问。

将策略映射到组 DN
-----------------

以下命令使用 :mc:`mc idp ldap policy attach` 将现有 MinIO :ref:`策略 <minio-policy>` 关联到 AD/LDAP 组 DN。

.. code-block:: shell

   mc idp ldap policy attach myminio consoleAdmin \
     --group='cn=ops,cn=groups,dc=example,dc=com'

   mc idp ldap policy attach myminio diagnostics \
     --group='cn=engineering,cn=groups,dc=example,dc=com'

- MinIO 会将 :userpolicy:`consoleAdmin` 策略分配给任何属于
  ``cn=ops,cn=groups,dc=example,dc=com`` AD/LDAP 组的认证用户，从而授予其对
  MinIO 服务器的完全访问权限。

- MinIO 会将 :userpolicy:`diagnostics` 策略分配给任何属于
  ``cn=engineering,cn=groups,dc=example,dc=com`` AD/LDAP 组的认证用户，从而授予其对
  诊断性管理操作的访问权限。
