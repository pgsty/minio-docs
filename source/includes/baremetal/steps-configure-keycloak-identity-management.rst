#. 配置或创建用于访问 Keycloak 的 Client

   认证到 Keycloak :guilabel:`Administrative Console`，然后进入 :guilabel:`Clients`。

   .. include:: /includes/common/common-configure-keycloak-identity-management-baremetal.rst
      :start-after: start-configure-keycloak-client
      :end-before: end-configure-keycloak-client

#. 为 MinIO Client 创建 Client Scope

   Client Scope 允许 Keycloak 在认证请求返回的 JSON Web Token（JWT）中映射用户属性。
   这样 MinIO 就可以在向用户分配策略时引用这些属性。
   此步骤会创建在 Keycloak 认证成功后支持 MinIO 授权所需的 Client Scope。

   .. include:: /includes/common/common-configure-keycloak-identity-management-baremetal.rst
      :start-after: start-configure-keycloak-client-scope
      :end-before: end-configure-keycloak-client-scope

#. 将所需属性应用到 Keycloak Users/Groups

   必须为 Keycloak Users 或 Groups 分配一个名为 ``policy`` 的属性。
   将其值设置为 MinIO 部署上的任意 :ref:`policy <minio-policy>`。

   .. include:: /includes/common/common-configure-keycloak-identity-management-baremetal.rst
      :start-after: start-configure-keycloak-user-group-attributes
      :end-before: end-configure-keycloak-user-group-attributes

#. 为 Keycloak 认证配置 MinIO

   MinIO 支持多种配置 Keycloak 认证的方法：

   - 使用终端/shell 以及 :mc:`mc idp openid` 命令
   - 使用在启动 MinIO 之前设置的环境变量

   .. tab-set::

      .. tab-item:: CLI

         .. include:: /includes/common/common-configure-keycloak-identity-management-baremetal.rst
            :start-after: start-configure-keycloak-minio-cli
            :end-before: end-configure-keycloak-minio-cli

      .. tab-item:: 环境变量

         .. include:: /includes/common/common-configure-keycloak-identity-management-baremetal.rst
            :start-after: start-configure-keycloak-minio-envvar
            :end-before: end-configure-keycloak-minio-envvar

   重启 MinIO 部署以应用这些更改。

   检查 MinIO 日志，确认启动成功，且没有与 OIDC 配置相关的错误。

   如果尝试通过 Console 登录，现在应能看到一个使用已配置 :guilabel:`Display Name` 的（SSO）按钮。

   指定一个已配置的用户并尝试登录。
   MinIO 应自动将您重定向到 Keycloak 登录页面。
   认证成功后，Keycloak 应使用原始 Console URL 或已配置的 :guilabel:`Redirect URI`
   将您重定向回 MinIO Console。

#. 使用 Security Token Service（STS）生成应用程序凭证

   .. include:: /includes/common/common-configure-keycloak-identity-management-baremetal.rst
      :start-after: start-configure-keycloak-sts
      :end-before: end-configure-keycloak-sts

#. 后续步骤

   应用程序应使用所选 :ref:`SDK <minio-drivers>`
   实现 :ref:`STS AssumeRoleWithWebIdentity <minio-sts-assumerolewithwebidentity>` 流程。
   当 STS 凭证过期时，应用程序应具备在重试并继续操作之前重新生成 JWT token、
   STS token 和 MinIO 凭证的逻辑。

   或者，用户也可以通过 MinIO Console 使用其 Keycloak 凭证生成
   :ref:`access keys <minio-id-access-keys>`，
   以创建类似长期 API key 的访问方式。
