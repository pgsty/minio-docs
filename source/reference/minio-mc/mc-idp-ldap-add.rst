.. _minio-mc-idp-ldap-add:

===================
``mc idp ldap add``
===================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc idp ldap add


描述
----

.. start-mc-idp-ldap-add-desc

:mc:`mc idp ldap add` 命令用于创建 AD/LDAP IDP 服务器配置。

.. end-mc-idp-ldap-add-desc

每个 MinIO 部署最多仅支持 *一个* (1) AD/LDAP 提供商。

.. tab-set::

   .. tab-item:: 示例

      以下示例为 ``myminio`` 部署设置 AD/LDAP 配置项。

      .. code-block:: shell
         :class: copyable

         mc idp ldap add                                                            \
                     myminio                                                        \
                     server_addr=myldapserver:636                                   \
                     lookup_bind_dn=cn=admin,dc=min,dc=io                           \
                     lookup_bind_password=somesecret                                \
                     user_dn_search_base_dn=dc=min,dc=io                            \
                     user_dn_search_filter="(uid=%s)"                               \
                     group_search_base_dn=ou=swengg,dc=min,dc=io                    \
                     group_search_filter="(&(objectclass=groupofnames)(member=%d))"

   .. tab-item:: 语法

      该命令的语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] idp ldap add               \
                                   ALIAS             \
                                   [CFG_PARAM1]      \
                                   [CFG_PARAM2]...

      - 将 ``ALIAS`` 替换为 MinIO 部署的 :ref:`alias <alias>`，用于创建 AD/LDAP 集成。
      - 将 ``[CFG_PARAM#]`` 替换为各个 :ref:`configuration setting <minio-ldap-config-settings>` 键值对，格式为 ``PARAMETER="value"``。

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   要添加 AD/LDAP 集成的 MinIO 部署的 :ref:`alias <alias>`。

   例如：

   .. code-block:: none

      mc idp ldap add myminio                               \
                      server_addr=myldapserver:636          \
                      lookup_bind_dn=cn=admin,dc=min,dc=io  \
                      lookup_bind_password=somesecret       \
                      user_dn_search_base_dn=dc=min,dc=io   \
                      user_dn_search_filter="(uid=%s)"      \

.. include:: /includes/common-minio-ad-ldap-params.rst
   :start-after: start-minio-ad-ldap-params
   :end-before: end-minio-ad-ldap-params


全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals


行为
----

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
