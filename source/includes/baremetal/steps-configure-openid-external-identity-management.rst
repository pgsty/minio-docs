1. 设置 OpenID 配置项

   你可以通过环境变量*或*服务端运行时配置设置来配置
   :abbr:`OIDC (OpenID Connect)` 提供方。
   这两种方式都需要启动或重启 MinIO 部署后才能生效。
   以下选项卡分别给出了必需和可选环境变量及配置项的速查：

   .. tab-set::

      .. tab-item:: 环境变量

         MinIO 支持使用 :ref:`环境变量 <minio-server-envvar-external-identity-management-openid>`
         指定 :abbr:`OIDC (OpenID Connect)` 提供方设置。
         :mc:`minio server` 进程会在下一次启动时应用这些设置。
         对于分布式部署，请在所有节点上一致地使用*相同*的值设置这些变量。

         以下示例代码设置了外部身份管理所需的全部
         :abbr:`OIDC (OpenID Connect)` 相关环境变量。
         唯一必需的变量是 :envvar:`MINIO_IDENTITY_OPENID_CONFIG_URL`：

         .. code-block:: shell
            :class: copyable

            export MINIO_IDENTITY_OPENID_CONFIG_URL="https://openid-provider.example.net/.well-known/openid-configuration"
            export MINIO_IDENTITY_OPENID_CLIENT_ID="<string>"
            export MINIO_IDENTITY_OPENID_CLIENT_SECRET="<string>"
            export MINIO_IDENTITY_OPENID_CLAIM_NAME="<string>"
            export MINIO_IDENTITY_OPENID_CLAIM_PREFIX="<string>"
            export MINIO_IDENTITY_OPENID_SCOPES="<string>"
            export MINIO_IDENTITY_OPENID_REDIRECT_URI="<string>"
            export MINIO_IDENTITY_OPENID_COMMENT="<string>"

         将 ``MINIO_IDENTITY_OPENID_CONFIG_URL`` 替换为
         :abbr:`OIDC (OpenID Connect)` 提供方 discovery document 的 URL 端点。

         这些变量的完整文档请参阅
         :ref:`minio-server-envvar-external-identity-management-openid`

      .. tab-item:: 配置项

         MinIO 支持使用 :mc-conf:`配置项 <identity_openid>`
         指定 :abbr:`OIDC (OpenID Connect)` 提供方设置。
         :mc:`minio server` 进程会在下一次启动时应用这些设置。
         对于分布式部署，:mc:`mc admin config` 命令会将配置应用到部署中的所有节点。

         以下示例代码设置了外部身份管理所需的全部
         :abbr:`OIDC (OpenID Connect)` 相关配置项。
         唯一必需的设置是
         :mc-conf:`identity_openid config_url <identity_openid.config_url>`：

         .. code-block:: shell
            :class: copyable

            mc admin config set ALIAS/ identity_openid \
               config_url="https://openid-provider.example.net/.well-known/openid-configuration" \
               client_id="<string>" \
               client_secret="<string>" \
               claim_name="<string>" \
               claim_prefix="<string>" \
               scopes="<string>" \
               redirect_uri="<string>" 

         将 ``config_url`` 替换为
         :abbr:`OIDC (OpenID Connect)` 提供方 discovery document 的 URL 端点。

         这些设置的完整文档请参阅 :mc-conf:`identity_openid`。

#. 重启 MinIO 部署

   你必须重启 MinIO 部署，才能应用这些配置更改。
   使用 :mc-cmd:`mc admin service restart` 命令重启部署。

   .. code-block:: shell
      :class: copyable

      mc admin service restart ALIAS

   将 ``ALIAS`` 替换为要重启部署的 :ref:`alias <alias>`。

#. 使用 OIDC 凭证生成 S3 兼容临时凭证

   MinIO 要求客户端使用
   :s3-api:`AWS Signature Version 4 协议 <sig-v4-authenticating-requests.html>`
   进行认证，同时兼容已弃用的 Signature Version 2 协议。
   具体来说，客户端必须提供有效的 access key 和 secret key，
   才能访问任意 S3 或 MinIO 管理 API，例如 ``PUT``、``GET`` 和 ``DELETE``。

   应用程序可以按需使用
   :ref:`minio-sts-assumerolewithwebidentity` Security Token Service (STS) API 端点，
   以及 :abbr:`OIDC (OpenID Connect)` 提供方返回的 JSON Web Token (JWT)
   生成临时访问凭证。

   应用程序必须提供一个工作流，用于登录
   :abbr:`OIDC (OpenID Connect)` 提供方并获取与认证会话关联的
   JSON Web Token (JWT)。
   关于如何在认证成功后获取和解析 JWT token，请参阅提供方文档。
   MinIO 提供了示例 Go 应用程序
   :minio-git:`web-identity.go <minio/blob/master/docs/sts/web-identity.go>`，
   用于演示该工作流。

   应用程序获取 JWT token 后，使用 ``AssumeRoleWithWebIdentity`` 端点
   生成临时凭证：

   .. code-block:: shell
      :class: copyable

      POST https://minio.example.net?Action=AssumeRoleWithWebIdentity
      &WebIdentityToken=TOKEN
      &Version=2011-06-15
      &DurationSeconds=86400
      &Policy=Policy

   - 将 ``TOKEN`` 替换为上一步返回的 JWT token。
   - 将 ``DurationSeconds`` 替换为临时凭证过期前的秒数。
     上例指定为 ``86400`` 秒，也就是 24 小时。
   - 将 ``Policy`` 替换为内联、经过 URL 编码的 JSON :ref:`policy <minio-policy>`，
     以进一步限制这些临时凭证关联的权限。

     如果省略，则会使用与 OpenID 用户
     :ref:`policy claim <minio-external-identity-management-openid-access-control>`
     关联的 policy。

   API 响应为 XML 文档，其中包含 access key、secret key、session token 和过期时间。
   应用程序可使用 access key 和 secret key 访问 MinIO 并执行操作。

   参考文档请参阅 :ref:`minio-sts-assumerolewithwebidentity`。
