.. _minio-authenticate-using-keycloak:

=====================================
配置 MinIO 使用 Keycloak 进行认证
=====================================

.. default-domain:: minio


.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 1

概览
----

本流程将 MinIO 配置为通过 OpenID Connect (OIDC) 协议，使用 `Keycloak <https://www.keycloak.org/>`__ 作为外部 Identity Provider (IDP) 来认证用户。

本页提供了在 Kubernetes 和裸金属 基础设施上的 MinIO 部署中配置 OIDC 的流程。

请选择与你基础设施对应的标签页，在不同说明集之间切换。

.. tab-set::
   :class: parent-tab

   .. tab-item:: Kubernetes
      :sync: k8s

      对于使用 :ref:`MinIO Kubernetes Operator <minio-kubernetes>` 部署的 MinIO Tenant，本流程包括：

      - 配置 Keycloak 以配合 MinIO 认证与授权
      - 配置新的或现有的 MinIO Tenant，使其使用 Keycloak 作为 OIDC 提供方
      - 创建用于控制 Keycloak 认证用户访问权限的策略
      - 使用 SSO 和 Keycloak 托管身份登录 MinIO Tenant Console
      - 使用 ``AssumeRoleWithWebIdentity`` Security Token Service (STS) API 生成临时 S3 访问凭证

   .. tab-item:: 裸金属
      :sync: baremetal

      对于部署在裸金属基础设施上的 MinIO，本流程包括：

      - 配置 Keycloak 以配合 MinIO 认证与授权
      - 配置新的或现有的 MinIO 集群，使其使用 Keycloak 作为 OIDC 提供方
      - 创建用于控制 Keycloak 认证用户访问权限的策略
      - 使用 SSO 和 Keycloak 托管身份登录 MinIO Console
      - 使用 ``AssumeRoleWithWebIdentity`` Security Token Service (STS) API 生成临时 S3 访问凭证

本流程基于 Keycloak ``21.0.0`` 编写并完成测试。
这些说明也可能适用于其他 Keycloak 版本。
本流程假定你已经具备 Keycloak 的使用经验，并已阅读 `其文档 <https://www.keycloak.org/documentation>`__，以获取部署、配置和管理该服务的指导和最佳实践。

前提条件
--------

Keycloak 部署与 Realm 配置
~~~~~~~~~~~~~~~~~~~~~~~~~~

本流程假定你已经拥有一个现成的 Keycloak 部署，并且具备其管理访问权限。
具体来说，你必须拥有在该 Keycloak 部署上创建和配置 Realms、Clients、Client Scopes、Realm Roles、Users 和 Groups 的权限。

.. tab-set::

   .. tab-item:: Kubernetes
      :sync: k8s

      对于与 MinIO Tenant 位于同一 Kubernetes 集群内的 Keycloak 部署，本流程假定 Keycloak 与 MinIO 的 pods/services 之间具备双向访问能力。
      对于位于 Kubernetes 集群外部的 Keycloak 部署，本流程假定已经存在用于管理 MinIO Tenant 入站和出站访问的 Ingress、Load Balancer 或类似 Kubernetes 网络控制组件。


   .. tab-item:: 裸金属
      :sync: baremetal

      MinIO 部署必须与目标 OIDC 服务保持双向访问。

请确保每个打算与 MinIO 配合使用的用户身份，都配置了合适的 :ref:`claim <minio-external-identity-management-openid-access-control>`，以便 MinIO 能将认证用户关联到某个 :ref:`策略 <minio-policy>`。
未分配任何策略的 OpenID 用户，无权访问 MinIO 集群中的任何操作或资源。


访问 MinIO 集群
~~~~~~~~~~~~~~~

.. tab-set::

   .. tab-item:: Kubernetes
      :sync: k8s

      你必须能够访问 MinIO Operator Console Web UI。
      你可以使用自己偏好的 Kubernetes 路由组件暴露 MinIO Operator Console Service，也可以通过临时端口转发，在本地机器上暴露 Console Service 端口。

   .. tab-item:: 裸金属
      :sync: baremetal

      本流程使用 :mc:`mc` 对 MinIO 集群执行操作。
      请在一台能够访问该集群网络的机器上安装 ``mc``。
      关于如何下载和安装 ``mc``，请参见 ``mc`` :ref:`安装快速开始 <mc-install>`。

      本流程假定已为 MinIO 集群配置 :mc:`alias <mc alias>`。

.. _minio-external-identity-management-keycloak-configure:

为 MinIO 配置 Keycloak 身份管理
-------------------------------

.. tab-set::

   .. tab-item:: Kubernetes
      :sync: k8s

      .. include:: /includes/k8s/steps-configure-keycloak-identity-management.rst

   .. tab-item:: 裸金属
      :sync: baremetal

      .. include:: /includes/baremetal/steps-configure-keycloak-identity-management.rst

启用 Keycloak Admin REST API
----------------------------

MinIO 支持使用 Keycloak Admin REST API 检查认证用户是否存在，*以及* 该用户是否在 Keycloak realm 中处于启用状态。
这一功能使 MinIO 可以更快地撤销此前已经认证过的 Keycloak 用户访问权限。
如果没有这项功能，MinIO 对已禁用或已删除用户最早能够生效的访问撤销时间点，只能等到最后一次获取的认证 token 过期。

本流程假定你已经拥有一个现成的 MinIO 部署，并已将 Keycloak 配置为外部身份管理器。

1) 创建所需的 Client Scopes
~~~~~~~~~~~~~~~~~~~~~~~~~~~

进入 :guilabel:`Client scopes` 视图并创建一个新的 scope：

.. list-table::
   :stub-columns: 1
   :widths: 30 70
   :width: 100%

   * - :guilabel:`Name`
     - 将 scope 名称设置为一个易于识别的名字（``minio-admin-API-access``）
   * - :guilabel:`Mappers`
     - 选择 :guilabel:`Configure a new mapper`
   * - :guilabel:`Audience`
     - 将 :guilabel:`Name` 设置为一个易于识别的映射名称（``minio-admin-api-access-mapper``）
   * - :guilabel:`Included Client Audience`
     - 设置为 ``security-admin-console``。

进入 :guilabel:`Clients`，并选择 MinIO client

1. 在 :guilabel:`Service account roles` 中，选择 :guilabel:`Assign role` 并分配 ``admin`` 角色
2. 在 :guilabel:`Client scopes` 中，选择 :guilabel:`Add client scope` 并添加前面创建的 scope

进入 :guilabel:`Settings`，并确保 :guilabel:`Authentication flow` 中包含 ``Service accounts roles``。

2) 验证 Admin API 访问
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

你可以使用 MinIO client 凭证调用 Admin REST API，以获取 bearer token 和用户数据，从而验证该功能：

1. 获取 bearer token：

   .. code-block:: shell
      :class: copyable

      curl -d "client_id=minio" \
           -d "client_secret=secretvalue" \
           -d "grant_type=password" \
           http://keycloak-url:port/admin/realms/REALM/protocol/openid-connect/token

2. 使用返回结果中的 ``access_token`` 访问 Admin API：

   .. code-block:: shell
      :class: copyable

      curl -H "Authentication: Bearer ACCESS_TOKEN_VALUE" \
           http://keycloak-url:port/admin/realms/REALM/users/UUID

   将 ``UUID`` 替换为你要查询用户的唯一 ID。
   响应应类似如下：

   .. code-block:: json
      
      {
         "id": "954de141-781b-4eaf-81bf-bf3751cdc5f2",
         "createdTimestamp": 1675866684976,
         "username": "minio-user-1",
         "enabled": true,
         "totp": false,
         "emailVerified": false,
         "firstName": "",
         "lastName": "",
         "attributes": {
            "policy": [
               "readWrite"
            ]
         },
         "disableableCredentialTypes": [],
         "requiredActions": [],
         "notBefore": 0,
         "access": {
            "manageGroupMembership": true,
            "view": true,
            "mapRoles": true,
            "impersonate": true,
            "manage": true
         }
      }

   如果返回值中的 enabled 为 false 或 null（用户已从 Keycloak 中移除），MinIO 会撤销该认证用户的访问权限。

3) 在 MinIO 上启用 Keycloak Admin 支持
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

MinIO 支持多种方式来配置 Keycloak Admin API 支持：

- 使用终端 / shell 以及 :mc:`mc idp openid` 命令
- 使用在启动 MinIO 之前设置的环境变量

.. tab-set::

   .. tab-item:: CLI

      你可以使用 :mc-cmd:`mc idp openid update` 命令修改现有 Keycloak 服务的配置项。
      如果是首次配置 Keycloak，也可以直接在初始设置时包含以下配置项。
      该命令接受所有受支持的 :ref:`OpenID 配置项 <minio-open-id-config-settings>`：

      .. code-block:: shell
         :class: copyable

         mc idp openid update ALIAS KEYCLOAK_IDENTIFIER \
            vendor="keycloak" \
            keycloak_admin_url="https://keycloak-url:port/admin"
            keycloak_realm="REALM"

      - 将 ``KEYCLOAK_IDENTIFIER`` 替换为已配置 Keycloak IDP 的名称。
        你可以使用 :mc-cmd:`mc idp openid ls` 查看 MinIO 部署上所有已配置的 IDP 项

      - 在 :mc-conf:`keycloak_admin_url <identity_openid.keycloak_admin_url>` 配置项中指定 Keycloak admin URL

      - 在 :mc-conf:`keycloak_realm <identity_openid.keycloak_realm>` 中指定 Keycloak Realm 名称

   .. tab-item:: Environment Variables

      在合适的配置位置（例如 ``/etc/default/minio``）设置以下 :ref:`环境变量 <minio-server-envvar-external-identity-management-openid>`。

      以下示例代码设置了在现有 Keycloak 配置上启用 Keycloak Admin API 所需的最小环境变量集合。
      请将后缀 ``_PRIMARY_IAM`` 替换为目标 Keycloak 配置的唯一标识符。

      .. code-block:: shell
         :class: copyable

         MINIO_IDENTITY_OPENID_VENDOR_PRIMARY_IAM="keycloak"
         MINIO_IDENTITY_OPENID_KEYCLOAK_ADMIN_URL_PRIMARY_IAM="https://keycloak-url:port/admin"
         MINIO_IDENTITY_OPENID_KEYCLOAK_REALM_PRIMARY_IAM="REALM"

      - 在 :envvar:`MINIO_IDENTITY_OPENID_KEYCLOAK_ADMIN_URL` 中指定 Keycloak admin URL
      - 在 :envvar:`MINIO_IDENTITY_OPENID_KEYCLOAK_REALM` 中指定 Keycloak Realm 名称
