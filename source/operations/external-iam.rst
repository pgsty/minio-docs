.. _minio-external-identity-management:

================
外部身份管理
================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

MinIO 支持通过以下 IDentity Providers (IDP) 对接多种外部身份管理器：

- :ref:`兼容 OpenID Connect <minio-external-iam-oidc>`
- :ref:`Active Directory / LDAP <minio-external-iam-ad-ldap>`

以下教程针对特定 IDP 软件提供了具体指导：

- :ref:`使用 Keycloak 配置 MinIO 认证 <minio-authenticate-using-keycloak>`

用户可以使用外部管理的凭证和相关的 :ref:`minio-security-token-service` API 对 MinIO 进行认证。
认证成功后，MinIO 会尝试将该用户与一个或多个已配置的 :ref:`策略 <minio-policy>` 关联。
未关联任何策略的用户，对 MinIO 部署不具备任何权限。

.. _minio-external-iam-oidc:

OpenID Connect (OIDC)
---------------------

MinIO 支持使用兼容 OpenID Connect (OIDC) 的 IDentity Provider (IDP) 来管理外部用户身份，例如 Okta、KeyCloak、Dex、Google 或 Facebook。
配置外部 :abbr:`IDP (IDentity Provider)` 后，即可启用 Single-Sign On 工作流，应用程序会先向外部 :abbr:`IDP (IDentity Provider)` 完成认证，再访问 MinIO。

MinIO 使用 :ref:`Policy Based Access Control (PBAC) <minio-access-management>` 来定义认证用户可访问的操作和资源。
MinIO 支持创建和管理可供外部身份用户声明使用的 :ref:`策略 <minio-policy>`。

对于由外部兼容 OpenID Connect (OIDC) 的提供方管理的身份，MinIO 使用 `JSON Web Token claim <https://datatracker.ietf.org/doc/html/rfc7519#section-4>`__ 来识别应分配给认证用户的 :ref:`策略 <minio-policy>`。

默认情况下，MinIO 会查找名为 ``policy`` 的 claim，并读取其中一个或多个策略名进行分配。
MinIO 会尝试将该 JWT claim 中列出的策略，与部署上已存在的策略进行匹配。
如果指定的策略在 MinIO 部署上一个都不存在，MinIO 会拒绝该用户发起的全部操作授权。
例如，考虑如下 claim 键值：

.. code-block:: shell

   policy="readwrite_data,read_analytics,read_logs"

该策略 claim 指示 MinIO 将名称分别匹配 ``readwrite_data``、``read_analytics`` 和 ``read_logs`` 的策略附加到认证用户。


你可以通过 :envvar:`MINIO_IDENTITY_OPENID_CLAIM_NAME` 环境变量设置自定义策略 claim，
*或者* 使用 :mc-cmd:`mc admin config set` 设置 :mc-conf:`identity_openid claim_name <identity_openid.claim_name>` 配置项。

关于如何将 MinIO 策略映射到 OIDC 托管身份，请参见 :ref:`minio-external-identity-management-openid-access-control`。

你可以使用 `JWT Debugging tool <https://jwt.io/>`__ 对返回的 JWT token 进行解码，并验证用户属性中是否包含指定 claim。
关于 JWT claim 的更多信息，请参见 `RFC 7519: JWT Claim <https://datatracker.ietf.org/doc/html/rfc7519#section-4>`__。
关于如何配置用户 claim，请以所选 OIDC 提供方的文档为准。

.. _minio-external-iam-ad-ldap:

Active Directory / LDAP
-----------------------

MinIO 支持使用 Active Directory 或 LDAP (AD/LDAP) 服务对用户身份进行外部管理。
配置外部 IDentity Provider (IDP) 后，即可启用 Single-Sign On (SSO) 工作流，应用程序会先向外部 IDP 认证，再访问 MinIO。

.. _minio-external-identity-management-ad-ldap-lookup-bind:

查询 Active Directory / LDAP 服务
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

MinIO 会查询已配置的 Active Directory / LDAP 服务器，以验证应用程序提供的凭证，并可选地返回该用户所属组列表。
该过程称为 Lookup-Bind 模式，它使用一个权限最小化的 AD/LDAP 用户，仅用于在 AD/LDAP 服务器上执行用户与组查询所需的认证。


以下标签页给出了启用 Lookup-Bind 模式所需环境变量和配置项的参考：

.. tab-set::

   .. tab-item:: Environment Variable

      - :envvar:`MINIO_IDENTITY_LDAP_LOOKUP_BIND_DN`
      - :envvar:`MINIO_IDENTITY_LDAP_LOOKUP_BIND_PASSWORD`
      - :envvar:`MINIO_IDENTITY_LDAP_USER_DN_SEARCH_BASE_DN`
      - :envvar:`MINIO_IDENTITY_LDAP_USER_DN_SEARCH_FILTER`

      关于这些变量的更多信息，请参见 :ref:`minio-server-envvar-external-identity-management-ad-ldap`
      参考文档。:ref:`minio-authenticate-using-ad-ldap-generic` 教程中包含这些值的完整配置说明。

   .. tab-item:: Configuration Setting

      - :mc-conf:`identity_ldap lookup_bind_dn <identity_ldap.lookup_bind_dn>`
      - :mc-conf:`identity_ldap lookup_bind_password <identity_ldap.lookup_bind_password>`
      - :mc-conf:`identity_ldap user_dn_search_base_dn <identity_ldap.user_dn_search_base_dn>`
      - :mc-conf:`identity_ldap user_dn_search_filter <identity_ldap.user_dn_search_filter>`

      关于这些设置的更多信息，请参见 :mc-conf:`identity_ldap` 参考文档。
      :ref:`minio-authenticate-using-ad-ldap-generic` 教程中包含这些变量的完整配置说明。

.. _minio-external-identity-management-ad-ldap-access-control:

AD/LDAP 托管身份的访问控制
~~~~~~~~~~~~~~~~~~~~~~~~~~

MinIO 使用 :ref:`Policy Based Access Control (PBAC) <minio-access-management>` 来定义认证用户可访问的操作和资源。
在使用 Active Directory/LDAP 服务器进行身份管理（认证）时，MinIO 仍通过 PBAC 来维护访问控制（授权）。

当用户使用其 AD/LDAP 凭证成功认证到 MinIO 后，MinIO 会搜索所有显式关联到该用户 Distinguished Name (DN) 的 :ref:`策略 <minio-policy>`。
具体来说，必须使用 :mc:`mc idp ldap policy attach` 命令，将策略分配给拥有匹配 DN 的用户。

MinIO 还支持查询用户的 AD/LDAP 组成员关系。
MinIO 会尝试将已存在的策略与用户所属各组的 DN 进行匹配。
认证用户的完整权限集合由显式分配策略和从组继承的策略共同组成。
更多信息请参见 :ref:`minio-external-identity-management-ad-ldap-access-control-group-lookup`。

MinIO 默认采用 deny-by-default 行为，即未显式分配任何策略、也未从组继承任何策略的用户，无法访问 MinIO 部署中的任何资源。

MinIO 提供 :ref:`内置策略 <minio-policy-built-in>` 以满足基础访问控制需求。
你可以使用 :mc:`mc admin policy create` 命令创建新策略。

.. _minio-external-identity-management-ad-ldap-access-control-group-lookup:

组查询
++++++++

MinIO 支持向 Active Directory / LDAP 服务器查询认证用户所属组列表。
MinIO 会尝试将现有 :ref:`策略 <minio-policy>` 与各组 DN 进行匹配，并将所有匹配到的策略分配给认证用户。

以下标签页给出了启用组查询所需环境变量和配置项的参考：

.. tab-set::

   .. tab-item:: Environment Variable

      - :envvar:`MINIO_IDENTITY_LDAP_GROUP_SEARCH_BASE_DN`
      - :envvar:`MINIO_IDENTITY_LDAP_GROUP_SEARCH_FILTER`

      关于这些变量的更多信息，请参见 :ref:`minio-server-envvar-external-identity-management-ad-ldap`
      参考文档。:ref:`minio-authenticate-using-ad-ldap-generic` 教程中包含这些值的完整配置说明。

   .. tab-item:: Configuration Setting


      - :mc-conf:`identity_ldap group_search_base_dn <identity_ldap.group_search_base_dn>`
      - :mc-conf:`identity_ldap group_search_filter <identity_ldap.group_search_filter>`

      关于这些设置的更多信息，请参见 :mc-conf:`identity_ldap` 参考文档。
      :ref:`minio-authenticate-using-ad-ldap-generic` 教程中包含这些变量的完整配置说明。

.. toctree::
   :glob:
   :hidden:

   /operations/external-iam/*
