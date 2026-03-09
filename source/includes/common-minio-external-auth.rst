.. Descriptions for External Identity Management using an OpenID Connect-compatible Provider
   Used in the following files:
   - /source/reference/minio-server/minio-server.rst
   - /source/reference/minio-cli/minio-mc-admin/mc-admin-config.rst
   - /source/security/identity-management/external-identity-management-openid/*


.. start-minio-openid-client-id

指定 MinIO 在使用与 :abbr:`OIDC (OpenID Connect)` 兼容的 provider 验证用户凭证时
所使用的唯一公开标识符。

.. end-minio-openid-client-id

.. start-minio-openid-client-secret

指定 MinIO 在使用与 :abbr:`OIDC (OpenID Connect)` 兼容的 provider 验证用户凭证时
所使用的 client secret。根据 provider 的不同，此字段可能是可选的。

.. versionchanged:: RELEASE.2023-06-23T20-26-00Z

   当通过 :mc-cmd:`mc admin config get` 返回此值时，MinIO 会将其脱敏。

.. end-minio-openid-client-secret


.. start-minio-openid-role-policy

指定逗号分隔的 :ref:`策略名称 <minio-policy>` 列表，用于该 provider 的所有认证请求
中的 ``RoleArn``。指定的一个或多个策略必须已经存在于 MinIO Server 上。

要使用此 OIDC 配置，你必须在 STS 请求体中指定对应的
:ref:`RoleArn <minio-assumerolewithwebidentity-query-parameters>`。

.. end-minio-openid-role-policy


.. start-minio-openid-jwks-url

指定 JSON Web Key Set (JWKS) 的 URL，MinIO 在验证与
:abbr:`OIDC (OpenID Connect)` 兼容的 provider 签发的 JSON Web Token (JWT) 时
会使用该 URL。

.. end-minio-openid-jwks-url

.. start-minio-openid-config-url

指定与 :abbr:`OIDC (OpenID Connect)` 兼容的 provider 的
`discovery document
<https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig>`__
URL。

:abbr:`OIDC (OpenID Connect)` Discovery URL 通常类似于：

``https://openid-provider.example.net/.well-known/openid-configuration``

.. end-minio-openid-config-url

.. start-minio-openid-claim-name

指定 `JWT Claim <https://datatracker.ietf.org/doc/html/rfc7519#section-4>`__
的名称，MinIO 使用该 Claim 来识别应附加到已认证用户上的
:ref:`策略 <minio-policy>`。

该 Claim 可以包含一个或多个逗号分隔的策略名称，以附加给用户。该 Claim *至少*
必须包含一个策略，否则该用户在 MinIO server 上将没有任何权限。

默认值为 ``policy``。

.. end-minio-openid-claim-name

.. start-minio-openid-display-name

指定 MinIO Console 在登录页面上显示给用户的名称。

.. end-minio-openid-display-name

.. start-minio-openid-claim-prefix

指定应用到所给 Claim 名称上的
`JWT Claim <https://datatracker.ietf.org/doc/html/rfc7519#section-4>`__
命名空间前缀。

.. end-minio-openid-claim-prefix

.. start-minio-openid-scopes

指定逗号分隔的
`scopes <https://datatracker.ietf.org/doc/html/rfc6749#section-3.3>`__ 列表。
默认使用 discovery document 中公布的 scopes。

.. end-minio-openid-scopes

.. start-minio-openid-redirect-uri

.. important::

   该参数已在 :minio-release:`RELEASE.2023-02-27T18-10-45Z` 中移除。
   请改用 :envvar:`MINIO_BROWSER_REDIRECT_URL`
   :ref:`环境变量 <minio-server-environment-variables>`。

MinIO Console 默认使用发起认证请求的节点主机名。
对于位于负载均衡器或反向代理之后的 MinIO 部署，请指定该字段，以确保 OIDC
provider 将认证响应返回到正确的 MinIO Console URL。
其中应包含 Console 主机名、端口以及 ``/oauth_callback``：

.. code-block:: shell

   http://minio.example.net:consoleport/oauth_callback

请确保在启动 MinIO Server 时使用 :mc-cmd:`~minio server --console-address`
选项，以设置固定的 Console 监听端口。
如果省略该选项，默认行为是在启动时随机选择端口号。

指定的 URI *必须* 与 provider 上批准的某个 redirect / callback URI 匹配。
更多信息请参见 OpenID
`Authentication Request <https://openid.net/specs/openid-connect-core-1_0.html#AuthRequest>`__。

.. end-minio-openid-redirect-uri

.. start-minio-openid-redirect-uri-dynamic

MinIO Console 默认会将发起认证请求的节点主机名作为重定向 URI 的一部分，
并将其提供给 OIDC provider。
对于位于使用轮询协议的负载均衡器之后的 MinIO 部署，这可能导致负载均衡器将响应
返回给与原始客户端不同的 MinIO 节点。

将此选项指定为 ``on``，可让 MinIO Console 使用原始请求中的 ``Host`` 头来构造
传递给 OIDC provider 的重定向 URI。
默认值为 ``off``。

.. end-minio-openid-redirect-uri-dynamic

.. start-minio-openid-claim-userinfo

允许 MinIO 从已认证用户的
`UserInfo Endpoint <https://openid.net/specs/openid-connect-core-1_0.html#UserInfo>`__
获取 claims。

有效值为 ``on`` 或 ``off``。

.. end-minio-openid-claim-userinfo

.. start-minio-openid-vendor

指定 OIDC Vendor，以启用该 vendor 的特定受支持行为。

支持以下值：

- ``keycloak``

.. end-minio-openid-vendor

.. start-minio-openid-keycloak-realm

指定用于 Keycloak Admin API 操作的 Keycloak Realm，例如 ``main``。

.. end-minio-openid-keycloak-realm

.. start-minio-openid-keycloak-admin-url

指定 Keycloak Admin API URL。
如果配置为定期校验已认证的 Keycloak 用户是否处于激活 / 存在状态，MinIO 可以
使用该 URL。
例如，``https://keycloak-endpoint:port/admin/``。

.. end-minio-openid-keycloak-admin-url

.. start-minio-openid-comment

指定要附加到与 :abbr:`OIDC (OpenID Connect)` 兼容 provider 配置上的注释。

.. end-minio-openid-comment

.. Descriptions for External Identity Management using an AD/LDAP Provider
   Used in the following files:
   - /source/reference/minio-server/minio-server.rst
   - /source/reference/minio-cli/minio-mc-admin/mc-admin-config.rst
   - /source/security/identity-management/ad-ldap-external-identity-management/*


.. start-minio-ad-ldap-server-addr

指定 Active Directory / LDAP server 的主机名。例如：

.. code-block:: shell
   :class: copyable

   ldapserver.com:636

.. admonition:: :mc-cmd:`~mc idp ldap add srv_record_name` 会自动识别端口
   :class: note

   如果你的 AD/LDAP server 使用
   :mc-cmd:`DNS SRV Records <mc idp ldap add srv_record_name>`，则 *不要* 在
   :mc-cmd:`~mc idp ldap add server_addr` 的值后追加端口号。
   SRV 请求在返回可用服务器列表时会自动包含端口号。
   
.. end-minio-ad-ldap-server-addr

.. start-minio-ad-ldap-lookup-bind-dn

指定 MinIO 在查询 AD/LDAP server 时所使用的 AD/LDAP 账户 Distinguished Name (DN)。
这会启用对 AD/LDAP server 的
:ref:`Lookup-Bind <minio-external-identity-management-ad-ldap-lookup-bind>`
认证。

该 DN 账户应为只读访问账号，并具备足够权限以支持执行用户和组查询。

.. end-minio-ad-ldap-lookup-bind-dn

.. start-minio-ad-ldap-lookup-bind-password

指定
:ref:`Lookup-Bind <minio-external-identity-management-ad-ldap-lookup-bind>`
用户账户的密码。

.. versionchanged:: RELEASE.2023-06-23T20-26-00Z

   当通过 :mc-cmd:`mc admin config get` 返回此值时，MinIO 会将其脱敏。

.. end-minio-ad-ldap-lookup-bind-password

.. start-minio-ad-ldap-user-dn-attributes

.. versionadded:: RELEASE.2024-06-06T09-36-42Z

用户 DN 属性的逗号分隔列表。

一些有效值包括 ``uid,cn,mail,sshPublicKey``。

如果要为 LDAP 用户启用公钥认证，请将 ``sshPublicKey`` 作为 DN 属性传入。
随后，用户即可使用传入的 SSH Public Key 登录 SFTP server。

.. code-block:: text
   :class: copyable

   mc idp ldap update ALIAS user_dn_attributes=sshPublicKey

.. end-minio-ad-ldap-user-dn-attributes

.. start-minio-ad-ldap-user-dn-search-base-dn

指定 MinIO 在查询与认证客户端所提供凭证相匹配的用户凭证时所使用的基础
Distinguished Name (DN)。

多个 DN 之间请使用分号（``;``）分隔。

例如：

.. code-block:: shell
   :class: copyable

   cn=miniousers,dc=myldapserver,dc=net;ou=swengg,dc=min,dc=io

支持 :ref:`Lookup-Bind <minio-external-identity-management-ad-ldap-lookup-bind>`
模式。

.. end-minio-ad-ldap-user-dn-search-base-dn

.. start-minio-ad-ldap-user-dn-search-filter

指定 MinIO 在查询与认证客户端所提供凭证相匹配的用户凭证时所使用的 AD/LDAP
搜索过滤器。

使用 ``%s`` 替换字符将客户端指定的用户名插入搜索字符串。例如：

.. code-block:: shell
   :class: copyable

   (userPrincipalName=%s)

.. end-minio-ad-ldap-user-dn-search-filter

.. start-minio-ad-ldap-group-search-filter

指定用于为已认证用户执行组查询的 AD/LDAP 搜索过滤器。

使用 ``%s`` 替换字符将客户端指定的用户名插入搜索字符串。
使用 ``%d`` 替换字符将客户端指定用户名对应的 Distinguished Name 插入搜索字符串。

例如：

.. code-block:: shell
   :class: copyable
   
   (&(objectclass=groupOfNames)(memberUid=%s))


在提供 AD/LDAP 组搜索过滤器时，应将其配置为仅返回支持认证所需的最少相关组。
返回大量组成员关系的过滤器会增加相关调用和资源的体量。
对大请求体或大响应体敏感的功能可能因此出现意外行为。


.. end-minio-ad-ldap-group-search-filter

.. start-minio-ad-ldap-group-search-base-dn

指定 MinIO 在执行组查询时所使用的组搜索基础
`Distinguished Names <https://learn.microsoft.com/en-us/previous-versions/windows/desktop/ldap/distinguished-names>`__
列表，多个值之间使用分号（``;``）分隔。
 
例如：

.. code-block:: shell
   :class: copyable
   
   cn=miniogroups,dc=myldapserver,dc=net;ou=swengg,dc=min,dc=io

.. end-minio-ad-ldap-group-search-base-dn

.. start-minio-ad-ldap-tls-skip-verify

指定 ``on`` 可在不验证的情况下信任 AD/LDAP server 的 TLS 证书。
如果 AD/LDAP server 的 TLS 证书由不受信任的证书颁发机构签名
（例如自签名），则可能需要此选项。

默认值为 ``off``

.. end-minio-ad-ldap-tls-skip-verify

.. start-minio-ad-ldap-server-insecure

指定 ``on`` 可允许与 AD/LDAP server 建立未加密的连接（非 TLS）。

MinIO 会以明文形式将 AD/LDAP 用户凭证发送到 AD/LDAP server，因此 *必须* 启用
TLS，才能防止凭证在传输过程中被读取。
启用此选项会带来安全风险，因为任何能够访问网络流量的用户都可能看到未加密的明文
凭证。

默认值为 ``off``。

.. end-minio-ad-ldap-server-insecure

.. start-minio-ad-ldap-server-starttls

指定 ``on`` 可启用到 AD/LDAP server 的 ``StartTLS`` 连接。

默认值为 ``off``

有关 ``StartTLS`` 的更多信息，请参见
`LDAP RFC 4511 specification <https://docs.ldap.com/specs/rfc4511.txt>`__
第 4.14 节。

.. end-minio-ad-ldap-server-starttls

.. start-minio-ad-ldap-srv_record_name

指定适当的值，以允许 MinIO 通过
`DNS SRV record <https://ldap.com/dns-srv-records-for-ldap>`__ 请求选择
AD/LDAP server。

启用后，MinIO 会通过以下方式选择 AD/LDAP server：

- 按标准命名约定构造目标 SRV 记录名称。
- 请求可用的 AD/LDAP server 列表。
- 根据优先级和权重选择合适的目标。

下面的配置示例假定 AD/LDAP server 地址设为 ``example.com``，SRV 记录协议为
``_tcp``。

对于以 ``_ldap`` 开头的 SRV 记录名称，指定 ``ldap``。
构造出的 DNS SRV 记录名称类似如下：

.. code-block:: shell

   _ldap._tcp.example.com

对于以 ``_ldaps`` 开头的 SRV 记录名称，指定 ``ldaps``。
构造出的 DNS SRV 记录名称类似如下：

.. code-block:: shell

   _ldaps._tcp.example.com

如果你的 DNS SRV 记录名称使用了其他 service 或 protocol 名称，请指定 ``on``，
并将完整记录名称作为 LDAP server 地址提供。
例如：``_ldapserver._specialtcp.example.com``

有关 DNS SRV 记录的更多信息，请参见
`DNS SRV Records for LDAP <https://ldap.com/dns-srv-records-for-ldap>`__。
 
.. admonition:: DNS SRV 记录配置中的 server 地址
   :class: important

   指定的 server 名称 **不得** 包含端口号。
   这与标准 AD/LDAP 配置不同，后者要求提供端口号。

   关于如何配置 AD/LDAP server 地址，请参见
   :mc-conf:`~identity_ldap.server_addr` 或
   :envvar:`MINIO_IDENTITY_LDAP_SERVER_ADDR`。

.. end-minio-ad-ldap-srv_record_name

.. start-minio-ad-ldap-comment

指定要附加到 AD/LDAP 配置上的注释。

.. end-minio-ad-ldap-comment

.. start-minio-ad-ldap-console-enable

#. 以 :ref:`root <minio-users-root>` 用户，或具有 :userpolicy:`consoleAdmin`
   策略的 MinIO 用户身份登录 MinIO Console。
#. 在 :guilabel:`Identity` 部分中，选择 :guilabel:`LDAP`，然后点击
   :guilabel:`Edit Configuration`，以配置 Active Directory 或 LDAP server。
   最低必需设置如下：

   - Server Address
   - Lookup Bind DN
   - Lookup Bind Password
   - User DN Search Base
   - User DN Search Filter

   并非所有配置项都可在 MinIO Console 中设置。
   如需更多设置，请使用 :mc:`mc idp ldap` 或
   :ref:`环境变量 <minio-server-envvar-external-identity-management-ad-ldap>`。
 
.. end-minio-ad-ldap-console-enable

.. start-minio-identity-management-plugin-url

外部身份管理服务的 webhook endpoint
（``https://authservice.example.net:8080/auth``）。

.. end-minio-identity-management-plugin-url

.. start-minio-identity-management-auth-token

提供给已配置 webhook endpoint 的认证令牌。

请以字符串形式指定受支持的 HTTP
`Authentication scheme <https://developer.mozilla.org/en-US/docs/Web/HTTP/Authentication#authentication_schemes>`__，
例如 ``"Bearer TOKEN"``。
MinIO 会通过 HTTP
`Authorization <https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Authorization>`__
头发送该令牌。

.. end-minio-identity-management-auth-token

.. start-minio-identity-management-role-policy

指定要分配给已认证用户的 MinIO :ref:`策略 <minio-policy>` 列表，多个策略之间
使用逗号分隔。

.. end-minio-identity-management-role-policy

.. start-minio-identity-management-role-id

指定 MinIO 用于为该 identity manager 生成 ARN 的唯一 ID。
在生成 ARN 时，MinIO 会自动在指定 ID 前添加 ``idmp-`` 前缀。

如果省略此项，MinIO 会自动生成该 ID，并将完整 ARN 输出到 server 日志中。

.. end-minio-identity-management-role-id

.. start-minio-identity-management-comment

指定要附加到身份配置上的注释。

.. end-minio-identity-management-comment

.. start-minio-access-management-plugin-url

外部访问管理服务的 webhook endpoint
（``https://authzservice.example.net:8080/authz``）。

.. end-minio-access-management-plugin-url

.. start-minio-access-management-plugin-auth-token

提供给已配置 webhook endpoint 的认证令牌。

请以字符串形式指定受支持的 HTTP
`Authentication scheme <https://developer.mozilla.org/en-US/docs/Web/HTTP/Authentication#authentication_schemes>`__，
例如 ``"Bearer TOKEN"``。
MinIO 会通过 HTTP
`Authorization <https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Authorization>`__
头发送该令牌。

.. end-minio-access-management-plugin-auth-token

.. start-minio-access-management-plugin-enable-http2

为连接已配置的 webhook 服务启用实验性的 HTTP2 支持。

默认值为 ``off``

.. end-minio-access-management-plugin-enable-http2

.. start-minio-access-management-plugin-comment

指定要附加到外部访问管理配置上的注释。

.. end-minio-access-management-plugin-comment
