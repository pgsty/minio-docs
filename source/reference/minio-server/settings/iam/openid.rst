.. _minio-server-envvar-external-identity-management-openid:
.. _minio-open-id-config-settings:

===================================
OpenID 身份管理设置
===================================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

本页面记录了如何使用与 OpenID Connect (OIDC) 兼容的提供方来启用外部身份管理的相关设置。
有关如何使用这些设置的教程，请参见 :ref:`minio-external-identity-management-openid`。

.. include:: /includes/common-mc-admin-config.rst
   :start-after: start-minio-settings-defined
   :end-before: end-minio-settings-defined

.. include:: /includes/common-mc-admin-config.rst
   :start-after: start-minio-settings-test-before-prod
   :end-before: end-minio-settings-test-before-prod

示例
----

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. code-block:: shell
         :class: copyable

         MINIO_IDENTITY_OPENID_CONFIG_URL="https://openid-provider.example.net/.well-known/openid-configuration"

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: identity_openid

      使用 :mc-cmd:`mc admin config set` 设置或更新 OpenID 配置。
      :mc-conf:`~identity_openid.config_url` 参数为*必需*。
      其他可选参数请以空白字符（``" "``）分隔的列表形式指定。

      .. code-block:: shell
         :class: copyable

         mc admin config set identity_openid                                               \
           config_url="https://openid-provider.example.net/.well-known/openid-configuration" \
           [ARGUMENT="VALUE"] ... 

设置
----

配置 URL
~~~~~~~~

*必需*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_IDENTITY_OPENID_CONFIG_URL

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: identity_openid config_url
         :delimiter: " "

.. include:: /includes/common-minio-external-auth.rst
   :start-after: start-minio-openid-config-url
   :end-before: end-minio-openid-config-url

启用
~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量

      此设置没有环境变量选项。
      请改用配置项。

   .. tab-item:: 配置项
      :selected:

      .. mc-conf:: identity_openid enabled
         :delimiter: " "


设置为 ``false`` 可禁用 OpenID 配置。

如果设置为 ``false``，应用程序将无法使用已配置的提供方生成 STS 凭证或通过其他方式向 MinIO 认证。

默认值为 ``true``（即“启用”）。

客户端 ID
~~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_IDENTITY_OPENID_CLIENT_ID

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: identity_openid client_id
         :delimiter: " "

.. include:: /includes/common-minio-external-auth.rst
   :start-after: start-minio-openid-client-id
   :end-before: end-minio-openid-client-id

客户端密钥
~~~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar
      
      .. envvar:: MINIO_IDENTITY_OPENID_CLIENT_SECRET

   .. tab-item:: 配置项
      :sync: config
      
      .. mc-conf:: identity_openid client_secret
         :delimiter: " "

.. include:: /includes/common-minio-external-auth.rst
   :start-after: start-minio-openid-client-secret
   :end-before: end-minio-openid-client-secret

角色策略
~~~~~~~~

*可选*

此设置与 ``Claim Name`` 设置互斥。

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_IDENTITY_OPENID_ROLE_POLICY

   .. tab-item:: 配置项

      .. mc-conf:: identity_openid role_policy
         :delimiter: " "

.. include:: /includes/common-minio-external-auth.rst
   :start-after: start-minio-openid-role-policy
   :end-before: end-minio-openid-role-policy

声明名称
~~~~~~~~

*可选*

此设置与 ``Role Policy`` 设置互斥。

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_IDENTITY_OPENID_CLAIM_NAME

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: identity_openid claim_name
         :delimiter: " "

.. include:: /includes/common-minio-external-auth.rst
   :start-after: start-minio-openid-claim-name
   :end-before: end-minio-openid-claim-name

声明前缀
~~~~~~~~

*可选*

此设置已弃用，并已在 :minio-release:`RELEASE.2024-07-13T01-46-15Z` 起移除。
请改用 :envvar:`MINIO_IDENTITY_OPENID_CLAIM_NAME`。

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar
      
      .. envvar:: MINIO_IDENTITY_OPENID_CLAIM_PREFIX

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: identity_openid claim_prefix
         :delimiter: " "

.. include:: /includes/common-minio-external-auth.rst
   :start-after: start-minio-openid-claim-prefix
   :end-before: end-minio-openid-claim-prefix

显示名称
~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_IDENTITY_OPENID_DISPLAY_NAME

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: identity_openid display_name
         :delimiter: " "

.. include:: /includes/common-minio-external-auth.rst
   :start-after: start-minio-openid-display-name
   :end-before: end-minio-openid-display-name

作用域
~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_IDENTITY_OPENID_SCOPES

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: identity_openid scopes
         :delimiter: " "

.. include:: /includes/common-minio-external-auth.rst
   :start-after: start-minio-openid-scopes
   :end-before: end-minio-openid-scopes

重定向 URI
~~~~~~~~~~

*可选*

此设置已弃用，并已在 :minio-release:`RELEASE.2024-07-13T01-46-15Z` 起移除。
请改用 :envvar:`MINIO_BROWSER_REDIRECT_URL`。

.. tab-set:: 

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_IDENTITY_OPENID_REDIRECT_URI

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: identity_openid redirect_uri
         :delimiter: " "

.. include:: /includes/common-minio-external-auth.rst
   :start-after: start-minio-openid-redirect-uri
   :end-before: end-minio-openid-redirect-uri

动态 URI 重定向
~~~~~~~~~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_IDENTITY_OPENID_REDIRECT_URI_DYNAMIC

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: identity_openid redirect_uri_dynamic
         :delimiter: " "

.. include:: /includes/common-minio-external-auth.rst
   :start-after: start-minio-openid-redirect-uri-dynamic
   :end-before: end-minio-openid-redirect-uri-dynamic

用户信息
~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_IDENTITY_OPENID_CLAIM_USERINFO

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: identity_openid claim_userinfo
         :delimiter: " "

.. include:: /includes/common-minio-external-auth.rst
   :start-after: start-minio-openid-claim-userinfo
   :end-before: end-minio-openid-claim-userinfo

提供方
~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_IDENTITY_OPENID_VENDOR

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: identity_openid vendor
         :delimiter: " "

.. include:: /includes/common-minio-external-auth.rst
   :start-after: start-minio-openid-vendor
   :end-before: end-minio-openid-vendor

Keycloak Realm
~~~~~~~~~~~~~~

*可选*

此设置要求将 ``OpenID Vendor`` 设置定义为 ``keycloak``。

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_IDENTITY_OPENID_KEYCLOAK_REALM

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: identity_openid keycloak_realm
         :delimiter: " "

.. include:: /includes/common-minio-external-auth.rst
   :start-after: start-minio-openid-keycloak-realm
   :end-before: end-minio-openid-keycloak-realm

Keycloak 管理 URL
~~~~~~~~~~~~~~~~~

*可选*

此设置要求将 ``OpenID Vendor`` 设置定义为 ``keycloak``。

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_IDENTITY_OPENID_KEYCLOAK_ADMIN_URL

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: identity_openid keycloak_admin_url
         :delimiter: " "

.. include:: /includes/common-minio-external-auth.rst
   :start-after: start-minio-openid-keycloak-admin-url
   :end-before: end-minio-openid-keycloak-admin-url

注释
~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_IDENTITY_OPENID_COMMENT

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: identity_openid comment
         :delimiter: " "

.. include:: /includes/common-minio-external-auth.rst
   :start-after: start-minio-openid-comment
   :end-before: end-minio-openid-comm
