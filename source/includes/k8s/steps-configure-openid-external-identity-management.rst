1. 访问 Operator Console

   临时在本地主机与 MinIO Operator Console 之间转发流量，
   并获取 Operator 部署的 JWT token。
   具体步骤请参阅
   :ref:`配置对 Operator Console 服务的访问 <minio-k8s-deploy-operator-access-console>`。

   在浏览器中打开临时 URL，并在登录页面输入 JWT Token。
   你应该会看到 :guilabel:`Tenants` 页面：

   .. image:: /images/k8s/operator-dashboard.png
      :align: center
      :width: 70%
      :class: no-scaled-link
      :alt: MinIO Operator Console

   如果要部署启用 OIDC 外部身份管理的新 MinIO Tenant，
   请选择 :guilabel:`+ Create Tenant` 按钮。

   如果要为现有 MinIO Tenant 配置 OIDC 外部身份管理，
   请从显示列表中选择该 Tenant。
   以下步骤将说明现有 Tenant 所需的配置区段和设置项。

#. 完成 :guilabel:`Identity Provider` 区段

   如需启用基于 OIDC 的外部身份管理，请选择 :guilabel:`Identity Provider` 区段。
   然后将单选按钮切换为 :guilabel:`OIDC`，以显示相关配置项。

   .. image:: /images/k8s/operator-create-tenant-identity-provider-openid.png
      :align: center
      :width: 70%
      :class: no-scaled-link
      :alt: MinIO Operator Console - Create a Tenant - External Identity Provider Section - OpenID

   星号 ``*`` 表示必填字段。
   下表给出了这些字段的一般说明：

   .. list-table::
      :header-rows: 1
      :widths: 40 60
      :width: 100%

      * - 字段
        - 说明

      * - Configuration URL
        - OpenID ``.well-known/openid-configuration`` 文件所在的主机名。

      * - | Client ID
          | Secret ID
        - MinIO 在向 OIDC 服务认证 OIDC 用户凭证时使用的 Client ID 和 Secret ID。

      * - Claim Name
        - MinIO 用于识别并附加到认证用户上的 :ref:`policy <minio-policy>` 的 OIDC Claim。

   完成该区段后，你可以继续填写
   :ref:`Tenant Deployment <minio-k8s-deploy-minio-tenant>` 的其他必需区段。

#. 为 OIDC 用户分配 Policy

   MinIO 默认不会为 OIDC 用户分配任何 :ref:`policy <minio-policy>`。
   MinIO 使用指定的用户 Claim 来识别并附加一个或多个 policy 到认证用户。
   如果 Claim 为空，或其中指定的 policy 在部署中不存在，
   则认证用户对该 Tenant 不具备任何权限。

   以下示例假定你已经为 MinIO Tenant 配置好了 :ref:`alias <alias>`。

   下面的示例 policy 仅对 ``data`` 存储桶授予通用 S3 API 访问权限：

   .. code-block:: json
      :class: copyable

      {
         "Version": "2012-10-17",
         "Statement": [
            {
               "Effect": "Allow",
               "Action": [
                  "s3:*"
               ],
               "Resource": [
                  "arn:aws:s3:::data",
                  "arn:aws:s3:::data/*"
               ]
            }
         ]
      }

   使用 :mc:`mc admin policy create` 命令，
   创建供 OIDC 用户使用的 policy：

   .. code-block:: shell
      :class: copyable

      mc admin policy create minio-tenant datareadonly /path/to/datareadonly.json

   只要认证后的 OIDC 用户在所配置的 claim 中包含 ``datareadonly``，
   MinIO 就会为其附加 ``datareadonly`` policy。

   关于 OIDC 用户和组的访问控制，请参阅
   :ref:`minio-external-identity-management-openid-access-control`。

#. 使用 OIDC 凭证生成 S3 兼容临时凭证

   应用程序可以按需使用
   :ref:`minio-sts-assumerolewithwebidentity`
   Security Token Service (STS) API 端点，
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

   - 将 ``minio.example.net`` 替换为 MinIO Tenant 服务的主机名或 URL。
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
