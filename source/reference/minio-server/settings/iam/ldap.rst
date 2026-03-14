.. _minio-server-envvar-external-identity-management-ad-ldap:
.. _minio-ldap-config-settings:

===========================
Active Directory / LDAP 设置
===========================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

本页面说明通过 Active Directory 或 LDAP 服务启用外部身份管理所需的设置。
有关使用这些设置的教程，请参见 :ref:`minio-authenticate-using-ad-ldap-generic`。

.. important:: 

   在 ``RELEASE.2023-05-26T23-31-54Z`` 版本中新增：

   相比使用配置设置，优先使用 :mc:`mc idp ldap` 命令将 MinIO 配置为使用 Active Directory 或 LDAP 进行身份管理。

   MinIO 建议使用 :mc:`mc idp ldap` 命令执行 LDAP 管理操作。
   这些命令提供了更好的校验能力和附加功能，同时提供与 ``identity_ldap`` 配置键相同的设置。
   有关 :mc:`mc idp ldap` 的使用教程，请参见 :ref:`minio-authenticate-using-ad-ldap-generic`。

``identity_ldap`` 配置设置仍可用于现有脚本和其他工具。

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

         MINIO_IDENTITY_LDAP_SERVER_ADDR="ldapserver.com:636"

      .. note::

         ``srv_record_name`` 会自动识别端口。

         如果你的 AD/LDAP 服务器使用 ``DNS SRV Records``，请 *不要* 在 ``server_addr`` 值后附加端口号。
         SRV 请求在返回可用服务器列表时会自动包含端口号。

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: identity_ldap

      使用 :mc:`mc admin config set` 定义 LDAP 时，以下设置为必需项：

      - ``enabled``
      - ``server_addr``
      - ``lookup_bind_dn``
      - ``lookup_bind_dn_password``
      - ``user_dn_search_base_dn``
      - ``user_dn_search_filter``

      .. code-block:: shell
         :class: copyable

         mc admin config set identity_ldap                        \
            enabled="true"                                        \
            server_addr="ad-ldap.example.net/"                    \
            lookup_bind_dn="cn=miniolookupuser,dc=example,dc=net" \
            lookup_bind_dn_password="userpassword"                \
            user_dn_search_base_dn="dc=example,dc=net"            \
            user_dn_search_filter="(&(objectCategory=user)(sAMAccountName=%s))"

设置
----

服务器地址
~~~~~~~~~~

*必需*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_IDENTITY_LDAP_SERVER_ADDR


         .. include:: /includes/common-minio-external-auth.rst
            :start-after: start-minio-ad-ldap-server-addr
            :end-before: end-minio-ad-ldap-server-addr

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: identity_ldap server_addr
         :delimiter: " "

.. include:: /includes/common-minio-external-auth.rst
   :start-after: start-minio-ad-ldap-server-addr
   :end-before: end-minio-ad-ldap-server-addr

Lookup Bind DN
~~~~~~~~~~~~~~

*必需*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_IDENTITY_LDAP_LOOKUP_BIND_DN

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: identity_ldap lookup_bind_dn
         :delimiter: " "

.. include:: /includes/common-minio-external-auth.rst
   :start-after: start-minio-ad-ldap-lookup-bind-dn
   :end-before: end-minio-ad-ldap-lookup-bind-dn

Lookup Bind 密码
~~~~~~~~~~~~~~~~

*必需*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_IDENTITY_LDAP_LOOKUP_BIND_PASSWORD

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: identity_ldap lookup_bind_password
         :delimiter: " "

.. include:: /includes/common-minio-external-auth.rst
   :start-after: start-minio-ad-ldap-lookup-bind-password
   :end-before: end-minio-ad-ldap-lookup-bind-password

用户 DN 搜索基准 DN
~~~~~~~~~~~~~~~~~~~

*必需*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_IDENTITY_LDAP_USER_DN_SEARCH_BASE_DN

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: identity_ldap user_dn_search_base_dn
         :delimiter: " "


.. include:: /includes/common-minio-external-auth.rst
   :start-after: start-minio-ad-ldap-user-dn-search-base-dn
   :end-before: end-minio-ad-ldap-user-dn-search-base-dn

用户 DN 搜索过滤器
~~~~~~~~~~~~~~~~~~

*必需*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_IDENTITY_LDAP_USER_DN_SEARCH_FILTER

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: identity_ldap user_dn_search_filter
         :delimiter: " "

.. include:: /includes/common-minio-external-auth.rst
   :start-after: start-minio-ad-ldap-user-dn-search-filter
   :end-before: end-minio-ad-ldap-user-dn-search-filter

用户 DN 属性
~~~~~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_IDENTITY_LDAP_USER_DN_ATTRIBUTES

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: identity_ldap user_dn_attributes
         :delimiter: " "

.. include:: /includes/common-minio-external-auth.rst
   :start-after: start-minio-ad-ldap-user-dn-attributes
   :end-before: end-minio-ad-ldap-user-dn-attributes

启用
~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量

      此设置没有环境变量选项。
      请改用配置设置。

   .. tab-item:: 配置设置
      :selected:

      .. mc-conf:: identity_ldap enabled
         :delimiter: " "

将其设置为 ``false`` 可禁用 AD/LDAP 配置。

若为 ``false``，应用程序将无法生成 STS 凭证，也无法通过已配置的提供者向 MinIO 进行身份验证。

默认为 ``true``（即 "enabled"）。

组搜索过滤器
~~~~~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_IDENTITY_LDAP_GROUP_SEARCH_FILTER

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: identity_ldap group_search_filter
         :delimiter: " "

.. include:: /includes/common-minio-external-auth.rst
   :start-after: start-minio-ad-ldap-group-search-filter
   :end-before: end-minio-ad-ldap-group-search-filter

在提供 AD/LDAP 组搜索过滤器时，应配置一个仅返回满足身份验证需求的最小相关组集合的过滤器。
返回大量组分配信息的过滤器会增加相关调用和资源消耗。
对大体积请求或响应体敏感的功能可能因此出现非预期行为。


组搜索基准 DN
~~~~~~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_IDENTITY_LDAP_GROUP_SEARCH_BASE_DN

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: identity_ldap group_search_base_dn
         :delimiter: " "

.. include:: /includes/common-minio-external-auth.rst
   :start-after: start-minio-ad-ldap-group-search-base-dn
   :end-before: end-minio-ad-ldap-group-search-base-dn

TLS 跳过校验
~~~~~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_IDENTITY_LDAP_TLS_SKIP_VERIFY

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: identity_ldap tls_skip_verify
         :delimiter: " "

.. include:: /includes/common-minio-external-auth.rst
   :start-after: start-minio-ad-ldap-tls-skip-verify
   :end-before: end-minio-ad-ldap-tls-skip-verify

服务器不安全模式
~~~~~~~~~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_IDENTITY_LDAP_SERVER_INSECURE

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: identity_ldap server_insecure
         :delimiter: " "

.. include:: /includes/common-minio-external-auth.rst
   :start-after: start-minio-ad-ldap-server-insecure
   :end-before: end-minio-ad-ldap-server-insecure

服务器 Start TLS
~~~~~~~~~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_IDENTITY_LDAP_SERVER_STARTTLS

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: identity_ldap server_starttls
         :delimiter: " "

.. include:: /includes/common-minio-external-auth.rst
   :start-after: start-minio-ad-ldap-server-starttls
   :end-before: end-minio-ad-ldap-server-starttls

SRV 记录名称
~~~~~~~~~~~~

*可选*

.. versionadded:: RELEASE.2022-12-12T19-27-27Z

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_IDENTITY_LDAP_SRV_RECORD_NAME

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: identity_ldap srv_record_name
         :delimiter: " "

.. include:: /includes/common-minio-external-auth.rst
   :start-after: start-minio-ad-ldap-srv_record_name
   :end-before: end-minio-ad-ldap-srv_record_name

备注
~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_IDENTITY_LDAP_COMMENT

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: identity_ldap identity_ldap comment
         :delimiter: " "

.. include:: /includes/common-minio-external-auth.rst
   :start-after: start-minio-ad-ldap-comment
   :end-before: end-minio-ad-ldap-comment
