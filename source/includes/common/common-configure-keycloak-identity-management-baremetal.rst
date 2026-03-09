.. start-configure-keycloak-client

选择 :guilabel:`Create client`，按照提示为 MinIO 创建一个新的 Keycloak client。
按如下方式填写指定输入项：

.. list-table::
   :stub-columns: 1
   :widths: 30 70
   :width: 100%

   * - :guilabel:`Client ID`
     - 设置为 MinIO 的唯一标识符（``minio``）
   * - :guilabel:`Client type`
     - 设置为 ``OpenID Connect``
   * - :guilabel:`Always display in console`
     - 切换为 ``On``
   * - :guilabel:`Client authentication`
     - 切换为 ``On``
   * - :guilabel:`Authentication flow`
     - 打开 ``Standard flow``
   * - （可选）:guilabel:`Authentication flow`
     - 打开 ``Direct access grants`` （API 测试）

Keycloak 会以一组默认配置值部署该 client。
请根据你的 Keycloak 环境和期望行为按需修改这些值。
下表提供了一组可用于配置的基线设置和值：

.. list-table::
   :stub-columns: 1
   :widths: 30 70
   :width: 100%

   * - :guilabel:`Root URL`
     - 设置为 ``${authBaseUrl}``
   * - :guilabel:`Home URL`
     - 设置为 MinIO 要使用的 Realm（``/realms/master/account/``）
   * - :guilabel:`Valid Redirect URI`
     - 设置为 ``*``
   * - :guilabel:`Keys -> Use JWKS URL`
     - 切换为 ``On``
   * - :guilabel:`Advanced -> Advanced Settings -> Access Token Lifespan`
     - 设置为 ``1 Hour``。

.. end-configure-keycloak-client

.. start-configure-keycloak-client-scope

进入 :guilabel:`Client scopes` 视图，为 MinIO 授权创建一个新的 client scope：

.. list-table::
   :stub-columns: 1
   :widths: 30 70
   :width: 100%

   * - :guilabel:`Name`
     - 设置为任意易识别的策略名称（``minio-authorization``）
   * - :guilabel:`Include in token scope`
     - 切换为 ``On``

创建完成后，从列表中选中该 scope，并进入 :guilabel:`Mappers`。

选择 :guilabel:`Configure a new mapper` 创建新的映射：

.. list-table::
   :stub-columns: 1
   :widths: 30 70
   :width: 100%

   * - :guilabel:`User Attribute`
     - 选择 Mapper Type
   * - :guilabel:`Name`
     - 设置为任意易识别的映射名称（``minio-policy-mapper``）
   * - :guilabel:`User Attribute`
     - 设置为 ``policy``
   * - :guilabel:`Token Claim Name`
     - 设置为 ``policy``
   * - :guilabel:`Add to ID token`
     - 设置为 ``On``
   * - :guilabel:`Claim JSON Type`
     - 设置为 ``String``
   * - :guilabel:`Multivalued`
     - 设置为 ``On``

       这样可以在单个 claim 中设置多个 ``policy`` 值。
   * - :guilabel:`Aggregate attribute values`
     - 设置为 ``On``

       这样用户就可以继承其所属 Group 中设置的任意 ``policy``。

创建完成后，将该 Client Scope 分配给 MinIO client。

1. 进入 :guilabel:`Clients` 并选择 MinIO client。
2. 选择 :guilabel:`Client scopes`，然后选择 :guilabel:`Add client scope`。
3. 选择先前创建的 scope，并将 :guilabel:`Assigned type` 设置为 ``default``。

.. end-configure-keycloak-client-scope

.. start-configure-keycloak-user-group-attributes

对于 Users，进入 :guilabel:`Users` 并选择或创建 User：

.. list-table::
   :stub-columns: 1
   :widths: 30 70
   :width: 100%

   * - :guilabel:`Credentials`
     - 如果尚未设置，将用户密码设置为永久值
   * - :guilabel:`Attributes`
     - 创建一个新属性，键为 ``policy``，值为 MinIO 部署上的任意 :ref:`policy <minio-policy>` （``consoleAdmin``）

对于 Groups，进入 :guilabel:`Groups` 并选择或创建 Group：

.. list-table::
   :stub-columns: 1
   :widths: 30 70
   :width: 100%

   * - :guilabel:`Attributes`
     - 创建一个新属性，键为 ``policy``，值为 MinIO 部署上的任意 :ref:`policy <minio-policy>` （``consoleAdmin``）

你可以将用户加入组，使其继承指定的 ``policy`` 属性。
如果 Mapper 设置中启用了 :guilabel:`Aggregate attribute values`，
Keycloak 会在已认证用户的 JWT token 中包含聚合后的策略数组。
MinIO 可以在为用户授权时使用这份策略列表。

你可以使用 Keycloak API 测试某个用户配置的策略：

.. code-block:: shell
   :class: copyable
   :substitutions:

   curl -d "client_id=minio" \
        -d "client_secret=secretvalue" \
        -d "grant_type=password" \
        -d "username=minio-user-1" \
        -d "password=minio-user-1-password" \
        http://keycloak-url.example.net:8080/realms/REALM/protocol/openid-connect/token

如果成功，``access_token`` 中会包含调用 MinIO :ref:`minio-sts-assumerolewithwebidentity`
STS API 并生成 S3 凭证所需的 JWT。

你可以使用 JWT 解码器检查其载荷，确认其中包含 ``policy`` 键，
且列出了一个或多个 MinIO policy。

.. end-configure-keycloak-user-group-attributes

.. start-configure-keycloak-sts

使用 S3 兼容 SDK 的应用程序必须以 Access Key 和 Secret Key 的形式提供凭证。
MinIO :ref:`minio-sts-assumerolewithwebidentity` API
会使用 Keycloak 在认证后返回的 JWT 返回所需的临时凭证，
其中包括必须提供的 session token。

你可以使用以下 HTTP 调用序列和 ``curl`` 工具测试这一工作流：

1. 以 Keycloak 用户身份认证并获取 JWT token

   .. code-block:: shell
      :class: copyable
      :substitutions:

      curl -X POST "https://keycloak-url.example.net:8080/realms/REALM/protocol/openid-connect/token" \
           -H "Content-Type: application/x-www-form-urlencoded" \
           -d "username=USER" \
           -d "password=PASSWORD" \
           -d "grant_type=password" \
           -d "client_id=CLIENT" \
           -d "client_secret=SECRET"

   - 将 ``USER`` 和 ``PASSWORD`` 替换为 ``REALM`` 中某个 Keycloak 用户的凭证。
   - 将 ``CLIENT`` 和 ``SECRET`` 替换为该 ``REALM`` 中 MinIO 专用 Keycloak client 的 ID 和 secret。

   你可以使用 ``jq`` 或类似的 JSON 格式化工具处理返回结果。
   提取 ``access_token`` 字段以获取所需的访问 token。
   同时关注 ``expires_in`` 字段，以了解 token 过期前还剩多少秒。

2. 使用 ``AssumeRoleWithWebIdentity`` API 生成 MinIO 凭证

   .. code-block:: shell
      :class: copyable
      :substitutions:

      curl -X POST "https://minio-url.example.net:9000" \
           -H "Content-Type: application/x-www-form-urlencoded" \
           -d "Action=AssumeRoleWithWebIdentity" \
           -d "Version=2011-06-15" \
           -d "DurationSeconds=86000" \
           -d "WebIdentityToken=TOKEN"

   将 ``TOKEN`` 替换为 Keycloak 返回的 ``access_token`` 值。

   API 成功时会返回一个 XML 文档，其中包含以下键：

   - ``Credentials.AccessKeyId`` - Keycloak 用户的 Access Key
   - ``Credentials.SecretAccessKey`` - Keycloak 用户的 Secret Key
   - ``Credentials.SessionToken`` - Keycloak 用户的 Session Token
   - ``Credentials.Expiration`` - 生成凭证的过期时间

3. 测试这些凭证

   使用你偏好的 S3 兼容 SDK，利用生成的凭证连接到 MinIO。

   例如，下面的 Python 代码使用 MinIO :ref:`Python SDK <minio-python-quickstart>`
   连接到 MinIO 部署并返回存储桶列表：

   .. code-block:: python
      :substitutions:

      from minio import Minio

      client = MinIO(
         "minio-url.example.net:9000",
         access_key = "ACCESS_KEY",
         secret_key = "SECRET_KEY",
         session_token = "SESSION_TOKEN"
         secure = True
      )

      client.list_buckets()

.. end-configure-keycloak-sts

.. start-configure-keycloak-minio-console

以 MinIO 部署的管理用户身份登录，例如拥有 :userpolicy:`consoleAdmin` policy 的用户。

从左侧导航栏选择 :guilabel:`Identity`，然后选择 :guilabel:`OpenID`。
选择 :guilabel:`Create Configuration` 创建一个新配置。

在弹窗中输入以下信息：

.. list-table::
   :stub-columns: 1
   :widths: 30 70
   :width: 100%

   * - :guilabel:`Name`
     - 为该 Keycloak 实例输入一个唯一名称

   * - :guilabel:`Config URL`
     - 指定 Keycloak OpenID 配置文档的地址（keycloak-url.example.net:8080）

       确保其中的 ``REALM`` 与你希望用于 MinIO 用户认证的 Keycloak realm 一致。

   * - :guilabel:`Client ID`
     - 指定在步骤 1 中创建的 Keycloak client 名称

   * - :guilabel:`Client Secret`
     - 指定在步骤 1 中创建的 Keycloak client secret 凭证值

   * - :guilabel:`Display Name`
     - 指定 MinIO Console 在单点登录（SSO）流程中向用户显示的该 Keycloak 服务名称

   * - :guilabel:`Scopes`
     - 指定要包含在 JWT 中的 OpenID scope，例如 ``preferred_username`` 或 ``email``

       你可以使用受支持的 OpenID 策略变量引用这些 scope，以便进行程序化策略控制。

   * - :guilabel:`Redirect URI Dynamic`
     - 切换为 ``on``

       这会将客户端所使用的 MinIO Console 地址替换到 Keycloak 重定向 URI 中。
       Keycloak 会使用提供的 URI 将已认证用户返回到 Console。

       对于位于反向代理、负载均衡器或类似网络控制平面之后的 MinIO Console 部署，
       你也可以使用 :envvar:`MINIO_BROWSER_REDIRECT_URL` 变量设置 Keycloak 应使用的重定向地址。

选择 :guilabel:`Save` 应用该配置。

.. end-configure-keycloak-minio-console

.. start-configure-keycloak-minio-cli

你可以使用 :mc-cmd:`mc idp openid add` 命令为 Keycloak 服务创建新配置。
该命令接受所有受支持的
:ref:`OpenID Configuration Settings <minio-open-id-config-settings>`：

.. code-block:: shell
   :class: copyable
   :substitutions:

   mc idp openid add ALIAS PRIMARY_IAM \
      client_id=MINIO_CLIENT \
      client_secret=MINIO_CLIENT_SECRET \
      config_url="https://keycloak-url.example.net:8080/realms/REALM/.well-known/openid-configuration" \
      display_name="SSO_IDENTIFIER"
      scopes="openid,email,preferred_username" \
      redirect_uri_dynamic="on"

.. list-table::
   :stub-columns: 1
   :widths: 30 70
   :width: 100%

   * - ``PRIMARY_IAM``
     - 设置为该 Keycloak 服务的唯一标识符，例如 ``keycloak_primary``

   * - | ``MINIO_CLIENT``
       | ``MINIO_CLIENT_SECRET``
     - 设置为步骤 1 中配置的 Keycloak client ID 和 secret

   * - ``config_url``
     - 设置为 Keycloak OpenID 配置文档的地址（keycloak-url.example.net:8080）

   * - ``display_name``
     - 设置为 MinIO Console 在单点登录（SSO）流程中向用户显示的该 Keycloak 服务名称

   * - ``scopes``
     - 设置为你希望包含在 JWT 中的 OpenID scope 列表，例如 ``preferred_username`` 或 ``email``

   * - ``redirect_uri_dynamic``
     - 设置为 ``on``

       这会将客户端所使用的 MinIO Console 地址替换到 Keycloak 重定向 URI 中。
       Keycloak 会使用提供的 URI 将已认证用户返回到 Console。

       对于位于反向代理、负载均衡器或类似网络控制平面之后的 MinIO Console 部署，
       你也可以使用 :envvar:`MINIO_BROWSER_REDIRECT_URL` 变量设置 Keycloak 应使用的重定向地址。

.. end-configure-keycloak-minio-cli

.. start-configure-keycloak-minio-envvar

在使用 ``-e ENVVAR=VALUE`` 标志启动容器之前，
设置以下 :ref:`环境变量 <minio-server-envvar-external-identity-management-openid>`。

下面的示例代码设置了将 Keycloak 配置为外部身份管理提供者所需的最小环境变量集合。

.. code-block:: shell
   :class: copyable
   :substitutions:

   MINIO_IDENTITY_OPENID_CONFIG_URL_PRIMARY_IAM="https://keycloak-url.example.net:8080/realms/REALM/.well-known/openid-configuration"
   MINIO_IDENTITY_OPENID_CLIENT_ID_PRIMARY_IAM="MINIO_CLIENT"
   MINIO_IDENTITY_OPENID_CLIENT_SECRET_PRIMARY_IAM="MINIO_CLIENT_SECRET"
   MINIO_IDENTITY_OPENID_DISPLAY_NAME_PRIMARY_IAM="SSO_IDENTIFIER"
   MINIO_IDENTITY_OPENID_SCOPES_PRIMARY_IAM="openid,email,preferred_username"
   MINIO_IDENTITY_OPENID_REDIRECT_URI_DYNAMIC_PRIMARY_IAM="on"

.. list-table::
   :stub-columns: 1
   :widths: 30 70
   :width: 100%

   * - ``_PRIMARY_IAM``
     - 将后缀 ``_PRIMARY_IAM`` 替换为该 Keycloak 配置的唯一标识符。
       例如，``MINIO_IDENTITY_OPENID_CONFIG_URL_KEYCLOAK_PRIMARY``。

       如果你只打算为该部署配置单个 OIDC 提供者，也可以省略该后缀。

   * - :envvar:`CONFIG_URL <MINIO_IDENTITY_OPENID_CONFIG_URL>`
     - 指定 Keycloak OpenID 配置文档的地址（keycloak-url.example.net:8080）

       确保其中的 ``REALM`` 与你希望用于 MinIO 用户认证的 Keycloak realm 一致

   * - | :envvar:`CLIENT_ID <MINIO_IDENTITY_OPENID_CLIENT_ID>`
       | :envvar:`CLIENT_SECRET <MINIO_IDENTITY_OPENID_CLIENT_SECRET>`

     - 指定步骤 1 中配置的 Keycloak client ID 和 secret

   * - :envvar:`DISPLAY_NAME <MINIO_IDENTITY_OPENID_DISPLAY_NAME>`
     - 指定 MinIO Console 在单点登录（SSO）流程中向用户显示的该 Keycloak 服务名称

   * - :envvar:`OPENID_SCOPES <MINIO_IDENTITY_OPENID_SCOPES>`

     - 指定你希望包含在 JWT 中的 OpenID scope，例如 ``preferred_username`` 或 ``email``

   * - :envvar:`REDIRECT_URI_DYNAMIC <MINIO_IDENTITY_OPENID_REDIRECT_URI_DYNAMIC>`
     - 设置为 ``on``

       这会将客户端所使用的 MinIO Console 地址替换到 Keycloak 重定向 URI 中。
       Keycloak 会使用提供的 URI 将已认证用户返回到 Console。

       对于位于反向代理、负载均衡器或类似网络控制平面之后的 MinIO Console 部署，
       你也可以使用 :envvar:`MINIO_BROWSER_REDIRECT_URL` 变量设置 Keycloak 应使用的重定向地址。

有关这些变量的完整文档，请参见 :ref:`minio-server-envvar-external-identity-management-openid`

.. end-configure-keycloak-minio-envvar
