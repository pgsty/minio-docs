1. 设置 Active Directory / LDAP 配置项

   你可以使用以下任一方式配置 AD/LDAP 提供方：

   * MinIO Client
   * 环境变量

   所有方式都需要启动或重启 MinIO 部署后才能生效。

   以下选项卡给出了可用配置方式的速查：

   .. tab-set::

      .. tab-item:: MinIO Client

         MinIO 支持使用 :mc:`mc idp ldap` 命令配置 AD/LDAP 提供方设置。

         对于分布式部署，:mc:`mc idp ldap` 命令会将配置应用到部署中的所有节点。

         以下示例代码设置了外部身份管理所需的全部 AD/LDAP 相关配置项。
         至少需要以下设置：

         - :mc-conf:`server_addr <identity_ldap.server_addr>`
         - :mc-conf:`lookup_bind_dn <identity_ldap.lookup_bind_dn>`
         - :mc-conf:`lookup_bind_password <identity_ldap.lookup_bind_password>`
         - :mc-conf:`user_dn_search_base_dn <identity_ldap.user_dn_search_base_dn>`
         - :mc-conf:`user_dn_search_filter <identity_ldap.user_dn_search_filter>`

         .. code-block:: shell
            :class: copyable

            mc idp ldap add ALIAS                                                  \
              server_addr="ldaps.example.net:636"                                  \
              lookup_bind_dn="CN=xxxxx,OU=xxxxx,OU=xxxxx,DC=example,DC=net"        \
              lookup_bind_password="xxxxxxxx"                                      \
              user_dn_search_base_dn="DC=example,DC=net"                           \
              user_dn_search_filter="(&(objectCategory=user)(sAMAccountName=%s))"  \
              group_search_filter= "(&(objectClass=group)(member=%d))"             \
              group_search_base_dn="ou=MinIO Users,dc=example,dc=net"              \
              tls_skip_verify="off"                                                \
              server_insecure=off                                                  \
              server_starttls="off"                                                \
              srv_record_name=""                                                   \
              comment="Test LDAP server"

         对于 Kubernetes 部署，请确保 `ALIAS` 对应 MinIO Tenant 的外部可访问主机名。

         这些设置的完整文档请参阅 :mc:`mc idp ldap`。

         .. admonition:: 建议使用 :mc:`mc idp ldap`
            :class: note

            相比 :mc-cmd:`mc admin config set` 运行时配置，
            :mc:`mc idp ldap` 提供了更多功能和更完善的校验能力。
            :mc:`mc idp ldap` 支持与 :mc:`mc admin config` 以及
            :mc-conf:`identity_ldap` 配置键相同的设置项。

            :mc-conf:`identity_ldap` 配置键仍可用于现有脚本和工具。

      .. tab-item:: 环境变量

         MinIO 支持使用 :ref:`环境变量 <minio-server-envvar-external-identity-management-ad-ldap>`
         指定 AD/LDAP 提供方设置。:mc:`minio server` 进程会在下一次启动时应用这些设置。
         对于分布式部署，请在部署中的所有节点上使用*相同*的值设置这些变量。
         节点之间的配置不一致会导致启动或配置失败。

         以下示例代码设置了外部身份管理所需的全部 AD/LDAP 相关环境变量。
         至少需要以下变量：

         - :envvar:`MINIO_IDENTITY_LDAP_SERVER_ADDR`
         - :envvar:`MINIO_IDENTITY_LDAP_LOOKUP_BIND_DN`
         - :envvar:`MINIO_IDENTITY_LDAP_LOOKUP_BIND_PASSWORD`
         - :envvar:`MINIO_IDENTITY_LDAP_USER_DN_SEARCH_BASE_DN`
         - :envvar:`MINIO_IDENTITY_LDAP_USER_DN_SEARCH_FILTER`

         .. code-block:: shell
            :class: copyable

            export MINIO_IDENTITY_LDAP_SERVER_ADDR="ldaps.example.net:636"
            export MINIO_IDENTITY_LDAP_LOOKUP_BIND_DN="CN=xxxxx,OU=xxxxx,OU=xxxxx,DC=example,DC=net"
            export MINIO_IDENTITY_LDAP_USER_DN_SEARCH_BASE_DN="dc=example,dc=net"
            export MINIO_IDENTITY_LDAP_USER_DN_SEARCH_FILTER="(&(objectCategory=user)(sAMAccountName=%s))"
            export MINIO_IDENTITY_LDAP_LOOKUP_BIND_PASSWORD="xxxxxxxxx"
            export MINIO_IDENTITY_LDAP_GROUP_SEARCH_FILTER="(&(objectClass=group)(member=%d))"
            export MINIO_IDENTITY_LDAP_GROUP_SEARCH_BASE_DN="ou=MinIO Users,dc=example,dc=net"
            export MINIO_IDENTITY_LDAP_TLS_SKIP_VERIFY="off"
            export MINIO_IDENTITY_LDAP_SERVER_INSECURE="off"
            export MINIO_IDENTITY_LDAP_SERVER_STARTTLS="off"
            export MINIO_IDENTITY_LDAP_SRV_RECORD_NAME=""
            export MINIO_IDENTITY_LDAP_COMMENT="LDAP test server"

         这些变量的完整文档请参阅 :ref:`minio-server-envvar-external-identity-management-ad-ldap`。

#. 重启 MinIO 部署

   你必须重启 MinIO 部署，才能应用这些配置更改。

   如果你是在 MinIO Console 中配置 AD/LDAP，则无需额外操作。
   MinIO Console 会在保存新的 AD/LDAP 配置后自动重启部署。

   对于 MinIO Client 和环境变量方式，请使用 :mc-cmd:`mc admin service restart`
   命令重启部署：

   .. code-block:: shell
      :class: copyable

      mc admin service restart ALIAS

   将 ``ALIAS`` 替换为要重启部署的 :ref:`alias <alias>`。

#. 使用 AD/LDAP 凭证登录 MinIO Console

   MinIO Console 支持完整工作流，包括向 AD/LDAP 提供方认证、
   使用 MinIO :ref:`minio-sts-assumerolewithldapidentity`
   Security Token Service (STS) 端点生成临时凭证，
   以及将用户登录到 MinIO 部署。

   你可以通过访问 MinIO 集群的根 URL 打开 Console，
   例如 ``https://minio.example.net:9000``。

   登录后，你可以执行该认证用户
   :ref:`被授权 <minio-external-identity-management-ad-ldap-access-control>`
   的任何操作。

   你还可以为必须在 MinIO 上执行操作的支持型应用程序创建
   :ref:`access key <minio-idp-service-account>`。
   Access Key 是长期有效的凭证，会继承父用户的权限。
   父用户还可以在创建服务账号时进一步限制这些权限。

#. 使用 AD/LDAP 凭证生成 S3 兼容临时凭证

   MinIO 要求客户端使用
   :s3-api:`AWS Signature Version 4 协议 <sig-v4-authenticating-requests.html>`
   进行认证，同时兼容已弃用的 Signature Version 2 协议。
   具体来说，客户端必须提供有效的 access key 和 secret key，
   才能访问任意 S3 或 MinIO 管理 API，例如 ``PUT``、``GET`` 和 ``DELETE``。

   应用程序可以按需使用
   :ref:`minio-sts-assumerolewithldapidentity` Security Token Service (STS) API 端点
   和 AD/LDAP 用户凭证生成临时访问凭证。
   MinIO 提供了示例 Go 应用程序
   :minio-git:`ldap.go <minio/blob/master/docs/sts/ldap.go>`，
   用于演示该工作流。

   .. code-block:: shell

      POST https://minio.example.net?Action=AssumeRoleWithLDAPIdentity
      &LDAPUsername=USERNAME
      &LDAPPassword=PASSWORD
      &Version=2011-06-15
      &Policy={}

   - 将 ``LDAPUsername`` 替换为 AD/LDAP 用户名。

   - 将 ``LDAPPassword`` 替换为 AD/LDAP 用户密码。

   - 将 ``Policy`` 替换为内联、经过 URL 编码的 JSON :ref:`policy <minio-policy>`，
     以进一步限制这些临时凭证关联的权限。

     如果省略，则会使用名称与 AD/LDAP 用户 Distinguished Name (DN)
     :ref:`匹配的 policy <minio-external-identity-management-ad-ldap-access-control>`。

   API 响应为 XML 文档，其中包含 access key、secret key、session token 和过期时间。
   应用程序可使用 access key 和 secret key 访问 MinIO 并执行操作。

   参考文档请参阅 :ref:`minio-sts-assumerolewithldapidentity`。
