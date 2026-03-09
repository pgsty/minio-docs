.. Descriptions for External Identity Management using an LDAP Provider
   Used in the following files:
   - /source/reference/minio-mc/mc-idp-ldap-add.rst
   - /source/reference/minio-mc/mc-idp-ldap-update.rst

   Does not include ALIAS, as the example differs between add and update

.. start-minio-ad-ldap-params

.. mc-cmd:: server_addr
   :required:

   .. include:: /includes/common-minio-external-auth.rst
      :start-after: start-minio-ad-ldap-server-addr
      :end-before: end-minio-ad-ldap-server-addr

   该参数对应环境变量 :envvar:`MINIO_IDENTITY_LDAP_SERVER_ADDR`。

.. mc-cmd:: lookup_bind_dn
   :required:

   .. include:: /includes/common-minio-external-auth.rst
      :start-after: start-minio-ad-ldap-lookup-bind-dn
      :end-before: end-minio-ad-ldap-lookup-bind-dn

   该参数对应环境变量 :envvar:`MINIO_IDENTITY_LDAP_LOOKUP_BIND_DN`。

.. mc-cmd:: lookup_bind_password
   :required:

   .. include:: /includes/common-minio-external-auth.rst
      :start-after: start-minio-ad-ldap-lookup-bind-password
      :end-before: end-minio-ad-ldap-lookup-bind-password

   该参数对应环境变量 :envvar:`MINIO_IDENTITY_LDAP_LOOKUP_BIND_PASSWORD`。

.. mc-cmd:: user_dn_attributes
   :optional:

   .. include:: /includes/common-minio-external-auth.rst
      :start-after: start-minio-ad-ldap-user-dn-attributes
      :end-before: end-minio-ad-ldap-user-dn-attributes

.. mc-cmd:: user_dn_search_base_dn
   :required:

   .. include:: /includes/common-minio-external-auth.rst
      :start-after: start-minio-ad-ldap-user-dn-search-base-dn
      :end-before: end-minio-ad-ldap-user-dn-search-base-dn

   该参数对应环境变量 :envvar:`MINIO_IDENTITY_LDAP_USER_DN_SEARCH_BASE_DN`。

.. mc-cmd:: user_dn_search_filter
   :required:

   .. include:: /includes/common-minio-external-auth.rst
      :start-after: start-minio-ad-ldap-user-dn-search-filter
      :end-before: end-minio-ad-ldap-user-dn-search-filter

   该参数对应环境变量 :envvar:`MINIO_IDENTITY_LDAP_USER_DN_SEARCH_FILTER`。

.. mc-cmd:: comment
   :optional:

   .. include:: /includes/common-minio-external-auth.rst
      :start-after: start-minio-ad-ldap-comment
      :end-before: end-minio-ad-ldap-comment

   该参数对应环境变量 :envvar:`MINIO_IDENTITY_LDAP_COMMENT`。

.. mc-cmd:: enabled
   :optional:

   设为 ``false`` 可禁用 AD/LDAP 配置。

   如果设为 ``false``，应用程序将无法生成 STS 凭证，
   也无法再使用已配置的提供者向 MinIO 进行身份验证。

   默认为 ``true``，即 "enabled"。

.. mc-cmd:: group_search_base_dn
   :optional:

   .. include:: /includes/common-minio-external-auth.rst
      :start-after: start-minio-ad-ldap-group-search-base-dn
      :end-before: end-minio-ad-ldap-group-search-base-dn

   该参数对应环境变量 :envvar:`MINIO_IDENTITY_LDAP_GROUP_SEARCH_BASE_DN`。

.. mc-cmd:: group_search_filter
   :optional:

   .. include:: /includes/common-minio-external-auth.rst
      :start-after: start-minio-ad-ldap-group-search-filter
      :end-before: end-minio-ad-ldap-group-search-filter

   该参数对应环境变量 :envvar:`MINIO_IDENTITY_LDAP_GROUP_SEARCH_FILTER`。

.. mc-cmd:: server_insecure
   :optional:

   .. include:: /includes/common-minio-external-auth.rst
      :start-after: start-minio-ad-ldap-server-insecure
      :end-before: end-minio-ad-ldap-server-insecure

   该参数对应环境变量 :envvar:`MINIO_IDENTITY_LDAP_SERVER_INSECURE`。

.. mc-cmd:: server_starttls
   :optional:

   .. include:: /includes/common-minio-external-auth.rst
      :start-after: start-minio-ad-ldap-server-starttls
      :end-before: end-minio-ad-ldap-server-starttls

   该参数对应环境变量 :envvar:`MINIO_IDENTITY_LDAP_SERVER_STARTTLS`。

.. mc-cmd:: srv_record_name
   :optional:

   .. versionadded:: RELEASE.2022-12-12T19-27-27Z

   .. include:: /includes/common-minio-external-auth.rst
      :start-after: start-minio-ad-ldap-srv_record_name
      :end-before: end-minio-ad-ldap-srv_record_name

   该参数对应环境变量 :envvar:`MINIO_IDENTITY_LDAP_SRV_RECORD_NAME`。

.. mc-cmd:: tls_skip_verify
   :optional:

   .. include:: /includes/common-minio-external-auth.rst
      :start-after: start-minio-ad-ldap-tls-skip-verify
      :end-before: end-minio-ad-ldap-tls-skip-verify

   该参数对应环境变量 :envvar:`MINIO_IDENTITY_LDAP_TLS_SKIP_VERIFY`。

.. end-minio-ad-ldap-params

.. Descriptions for adding LDAP access keys
   Used in the following files:
   - /source/reference/minio-mc/mc-idp-ldap-accesskey.rst
   - /source/reference/minio-mc/mc-idp-ldap-accesskey-info.rst
   - /source/reference/minio-mc/mc-idp-ldap-accesskey-rm.rst
   - /source/reference/minio-mc/mc-idp-ldap-accesskey-ls.rst

.. start-minio-ad-ldap-accesskey-creation

此命令适用于 AD/LDAP 用户在通过 MinIO 完成身份验证后创建的
:ref:`访问密钥 <minio-id-access-keys>`。

使用 :mc-cmd:`mc idp ldap accesskey create` 命令创建 AD/LDAP 服务账户。

MinIO 支持使用
:ref:`AssumeRoleWithLDAPIdentity <minio-sts-assumerolewithldapidentity>`
通过 :ref:`Security Token Service <minio-security-token-service>`
生成临时访问密钥。

.. end-minio-ad-ldap-accesskey-creation
