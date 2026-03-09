#. 访问 Operator Console

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

   如果要部署启用 AD/LDAP 外部身份管理的新 MinIO Tenant，
   请选择 :guilabel:`+ Create Tenant` 按钮。

   如果要为现有 MinIO Tenant 配置 AD/LDAP 外部身份管理，
   请从显示列表中选择该 Tenant。
   以下步骤将说明现有 Tenant 所需的配置区段和设置项。

#. 完成 :guilabel:`Identity Provider` 区段

   如需启用基于 Active Directory / LDAP 提供方的外部身份管理，
   请选择 :guilabel:`Identity Provider` 区段。
   然后将单选按钮切换为 :guilabel:`Active Directory`，
   以显示相关配置项。

   .. image:: /images/k8s/operator-create-tenant-identity-provider-adldap.png
      :align: center
      :width: 70%
      :class: no-scaled-link
      :alt: MinIO Operator Console - Create a Tenant - External Identity Provider Section - Active Directory / LDAP

   星号 ``*`` 表示必填字段。
   下表给出了这些字段的一般说明：

   .. list-table::
      :header-rows: 1
      :widths: 40 60
      :width: 100%

      * - 字段
        - 说明

      * - LDAP Server Address
        - Active Directory 或 LDAP 服务器的主机名。

      * - Lookup Bind DN
        - MinIO 用于向 AD/LDAP 服务器认证并执行查询的 Distinguished Name。

          更多信息请参阅 :ref:`minio-external-identity-management-ad-ldap-lookup-bind`。

      * - List of user DNs (Distinguished Names) to be Tenant Administrators
        - 指定用户 :abbr:`DNs (Distinguished Names)`，
          MinIO 会为这些用户分配具有 Tenant 管理权限的 :ref:`policy <minio-policy>`。
          你可以通过选择加号 :octicon:`plus-circle` 图标来指定多个 :abbr:`DNs (Distinguished Names)`。
          也可以通过选择对应 DN 的垃圾桶 :octicon:`trash` 图标删除该 DN。

   完成该区段后，你可以继续填写
   :ref:`Tenant Deployment <minio-k8s-deploy-minio-tenant>` 的其他必需区段。

#. 为 AD/LDAP 用户分配 Policy

   MinIO 默认不会为 AD/LDAP 用户或组分配任何 :ref:`policy <minio-policy>`。
   你必须显式将 MinIO policy 分配给指定用户或组的 Distinguished Name (DN)，
   才能授予该用户或组访问 MinIO 部署的权限。

   以下示例假定你已经为 MinIO Tenant 配置好了 :ref:`alias <alias>`。

   使用 :mc:`mc idp ldap policy attach` 命令，
   将用户或组 DN 绑定到现有 MinIO Policy：

   .. code-block:: shell
     :class: copyable

     mc idp ldap policy attach minio-tenant POLICY --user='uid=primary,cn=applications,dc=domain,dc=com'
     mc idp ldap policy attach minio-tenant POLICY --group='cn=applications,ou=groups,dc=domain,dc=com'

   将 ``POLICY`` 替换为要分配给该用户或组 DN 的 MinIO policy 名称。

   关于 AD/LDAP 用户和组的访问控制，请参阅
   :ref:`minio-external-identity-management-ad-ldap-access-control`。

#. 使用 AD/LDAP 凭证生成 S3 兼容临时凭证

   应用程序可以按需使用 AD/LDAP 用户凭证，
   通过 :ref:`minio-sts-assumerolewithldapidentity`
   Security Token Service (STS) API 端点生成 S3 兼容临时凭证。
   MinIO 提供了示例 Go 应用程序
   :minio-git:`ldap.go <minio/blob/master/docs/sts/ldap.go>`，
   用于演示该工作流。

   .. code-block:: shell

     POST https://minio.example.net?Action=AssumeRoleWithLDAPIdentity
     &LDAPUsername=USERNAME
     &LDAPPassword=PASSWORD
     &Version=2011-06-15
     &Policy={}

   - 将 ``minio.example.net`` 替换为 MinIO Tenant 服务的主机名或 URL。

   - 将 ``LDAPUsername`` 替换为 AD/LDAP 用户名。

   - 将 ``LDAPPassword`` 替换为 AD/LDAP 用户密码。

   - 将 ``Policy`` 替换为内联、经过 URL 编码的 JSON :ref:`policy <minio-policy>`，
     以进一步限制这些临时凭证关联的权限。

     如果省略，则会使用名称与 AD/LDAP 用户 Distinguished Name (DN)
     :ref:`匹配的 policy <minio-external-identity-management-ad-ldap-access-control>`。

   API 响应为 XML 文档，其中包含 access key、secret key、session token 和过期时间。
   应用程序可使用 access key 和 secret key 访问 MinIO 并执行操作。

   参考文档请参阅 :ref:`minio-sts-assumerolewithldapidentity`。
