.. |KEYCLOAK_URL| replace:: keycloak-service.keycloak-namespace.svc.cluster-domain.example
.. |MINIO_S3_URL| replace:: minio.minio-tenant.svc.cluster-domain.example
.. |MINIO_CONSOLE_URL| replace:: minio-console.minio-tenant.svc.cluster-domain.example

#. 配置或创建用于访问 Keycloak 的 Client

   认证到 Keycloak :guilabel:`Administrative Console`，然后进入 :guilabel:`Clients`。

   .. include:: /includes/common/common-configure-keycloak-identity-management.rst
      :start-after: start-configure-keycloak-client
      :end-before: end-configure-keycloak-client

#. 为 MinIO Client 创建 Client Scope

   Client scope 允许 Keycloak 在认证请求返回的 JSON Web Token（JWT）中映射用户属性。
   这样 MinIO 就可以在向用户分配策略时引用这些属性。
   这一步会创建在 Keycloak 认证成功后支持 MinIO 授权所需的 client scope。

   .. include:: /includes/common/common-configure-keycloak-identity-management.rst
      :start-after: start-configure-keycloak-client-scope
      :end-before: end-configure-keycloak-client-scope

#. 将所需属性应用到 Keycloak Users/Groups

   你必须为 Keycloak Users 或 Groups 分配一个名为 ``policy`` 的属性。
   将其值设置为 MinIO 部署上的任意 :ref:`policy <minio-policy>`。

   .. include:: /includes/common/common-configure-keycloak-identity-management.rst
      :start-after: start-configure-keycloak-user-group-attributes
      :end-before: end-configure-keycloak-user-group-attributes

#. 为 Keycloak 认证配置 MinIO

.. include:: /includes/common/common-configure-keycloak-identity-management.rst
   :start-after: start-configure-keycloak-minio-cli
   :end-before: end-configure-keycloak-minio-cli

重启 MinIO 部署以应用这些更改。

检查 MinIO 日志，确认启动成功，且没有与 OIDC 配置相关的错误。

#. 使用 Security Token Service（STS）生成应用程序凭证

   .. include:: /includes/common/common-configure-keycloak-identity-management.rst
      :start-after: start-configure-keycloak-sts
      :end-before: end-configure-keycloak-sts

#. 后续步骤

应用程序应使用其选择的 :ref:`SDK <minio-drivers>`
实现 :ref:`STS AssumeRoleWithWebIdentity <minio-sts-assumerolewithwebidentity>` 流程。
当 STS 凭证过期时，应用程序应具备在重试并继续操作之前重新生成 JWT token、
STS token 和 MinIO 凭证的逻辑。

或者，用户也可以通过 MinIO Console 使用其 Keycloak 凭证生成
:ref:`access keys <minio-id-access-keys>`，
以创建类似长期 API key 的访问方式。
