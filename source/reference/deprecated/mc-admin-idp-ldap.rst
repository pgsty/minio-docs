.. _minio-mc-admin-idp-ldap:

=====================
``mc admin idp ldap``
=====================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc admin idp ldap

.. versionchanged:: RELEASE.2023-05-26T23-31-54Z

   ``mc admin idp ldap`` 及其子命令已由 :mc-cmd:`mc idp ldap` 替代。

说明
----

.. start-mc-admin-idp-ldap-desc

:mc-cmd:`mc admin idp ldap` 命令允许你为第三方 :ref:`Active Directory 或 LDAP 身份与访问管理（IAM）集成 <minio-external-identity-management-ad-ldap>` 添加、修改、查看、列出、移除、启用和禁用服务器配置。

.. end-mc-admin-idp-ldap-desc

在 :ref:`配置 AD/LDAP 连接 <minio-authenticate-using-ad-ldap-generic>` 时，可将配置设置作为使用环境变量的替代方案。

.. note::

   配置设置**不会**覆盖通过环境变量配置的设置。


:mc-cmd:`mc admin idp ldap` 命令包含以下子命令：

.. list-table::
   :header-rows: 1
   :widths: 40 60

   * - 子命令
     - 说明

   * - :mc-cmd:`mc admin idp ldap add`
     - 创建 AD/LDAP IDP 服务器配置。

   * - :mc-cmd:`mc admin idp ldap update`
     - 修改现有的 AD/LDAP IDP 服务器配置。

   * - :mc-cmd:`mc admin idp ldap ls`
     - 列出 AD/LDAP 服务器配置。

   * - :mc-cmd:`mc admin idp ldap rm`
     - 从部署中移除 AD/LDAP IDP 服务器配置。

   * - :mc-cmd:`mc admin idp ldap info`
     - 显示指定 AD/LDAP 服务器配置的详细信息。

   * - :mc-cmd:`mc admin idp ldap enable`
     - 启用 AD/LDAP 服务器配置。

   * - :mc-cmd:`mc admin idp ldap disable`
     - 禁用 AD/LDAP 服务器配置。

   * - :mc-cmd:`mc admin idp ldap policy entities`
     - 列出策略关联实体。

配置参数
--------

:mc-cmd:`mc admin idp ldap` 子命令支持配置参数。
这些参数定义了服务器与 Active Directory 或 LDAP IAM 提供方的交互方式。

有关配置参数的更详细说明，请参阅 :ref:`配置设置文档 <minio-ldap-config-settings>`。

语法
----

.. mc-cmd:: add

   为 AD/LDAP 提供方创建新配置。
   每个部署中，MinIO 最多仅支持 *一个* (1) AD/LDAP 提供方。

   .. tab-set::

      .. tab-item:: EXAMPLE

         以下示例为 ``myminio`` 部署设置 AD/LDAP 配置参数。

         .. code-block:: shell
            :class: copyable

             mc admin idp ldap add                                               \
                  myminio                                                        \                                              
                  server_addr=myldapserver:636                                   \                                                       
                  lookup_bind_dn=cn=admin,dc=min,dc=io                           \                                               
                  lookup_bind_password=somesecret                                \                                                    
                  user_dn_search_base_dn=dc=min,dc=io                            \                                                
                  user_dn_search_filter="(uid=%s)"                               \                                                   
                  group_search_base_dn=ou=swengg,dc=min,dc=io                    \                                        
                  group_search_filter="(&(objectclass=groupofnames)(member=%d))"                                                          
                                    
      .. tab-item:: SYNTAX

         该命令语法如下：

         .. code-block:: shell
            :class: copyable

            mc [GLOBALFLAGS] admin idp ldap add          \
                                       ALIAS             \
                                       [CFG_PARAM1]      \
                                       [CFG_PARAM2]...

         - 将 ``ALIAS`` 替换为 MinIO 部署的 :ref:`alias <alias>`，用于创建 AD/LDAP 集成配置。
         - 将 ``[CFG_PARAM#]`` 替换为各个 :ref:`配置设置 <minio-ldap-config-settings>` 键值对，格式为 ``PARAMETER="value"``。

.. mc-cmd:: update

   修改 AD/LDAP 提供方现有的一组配置。

   .. tab-set::

      .. tab-item:: EXAMPLE

         以下示例修改 ``myminio`` 部署中的两个 AD/LDAP 配置参数。

         .. code-block:: shell
            :class: copyable

            mc admin idp ldap update                                \
                              myminio                               \
                              lookup_bind_dn=cn=admin,dc=min,dc=io  \
                              lookup_bind_password=somesecret                                                              
                                    
      .. tab-item:: SYNTAX

         该命令语法如下：

         .. code-block:: shell
            :class: copyable

            mc [GLOBALFLAGS] admin idp ldap update           \
                                            ALIAS            \
                                            [CFG_PARAM1]     \
                                            [CFG_PARAM2]...

         - 将 ``ALIAS`` 替换为 MinIO 部署的 :ref:`alias <alias>`，用于更新 AD/LDAP 集成配置。
         - 将 ``[CFG_PARAM#]`` 替换为要更新的各个 :ref:`配置设置 <minio-ldap-config-settings>` 键值对，格式为 ``PARAMETER="value"``。

.. mc-cmd:: ls, list

   列出 AD/LDAP 提供方现有的一组配置。

   .. tab-set::

      .. tab-item:: EXAMPLE

         以下示例列出 ``myminio`` 部署中的 AD/LDAP 配置参数。

         .. code-block:: shell
            :class: copyable

            mc admin idp ldap ls myminio                   
                                    
      .. tab-item:: SYNTAX

         该命令语法如下：

         .. code-block:: shell
            :class: copyable

            mc [GLOBALFLAGS] admin idp ldap ls ALIAS

         - 将 ``ALIAS`` 替换为 MinIO 部署的 :ref:`alias <alias>`，用于列出 AD/LDAP 集成配置。

.. mc-cmd:: rm, remove

   移除 AD/LDAP 提供方现有配置。

   .. tab-set::

      .. tab-item:: EXAMPLE

         以下示例移除 ``myminio`` 部署中的 AD/LDAP 提供方设置。

         .. code-block:: shell
            :class: copyable

            mc admin idp ldap rm myminio                                                             
                                    
      .. tab-item:: SYNTAX

         该命令语法如下：

         .. code-block:: shell
            :class: copyable

            mc [GLOBALFLAGS] admin idp ldap rm     \
                                            ALIAS

         - 将 ``ALIAS`` 替换为 MinIO 部署的 :ref:`alias <alias>`，用于移除 AD/LDAP 集成配置。


.. mc-cmd:: info

   输出指定 MinIO 部署上 AD/LDAP 提供方的当前配置。

   .. tab-set::

      .. tab-item:: EXAMPLE

         以下示例输出 ``myminio`` 部署上的 AD/LDAP 配置参数。

         .. code-block:: shell
            :class: copyable

            mc admin idp ldap info myminio
                                    
      .. tab-item:: SYNTAX

         该命令语法如下：

         .. code-block:: shell
            :class: copyable

            mc [GLOBALFLAGS] admin idp ldap info     \
                                            ALIAS

         - 将 ``ALIAS`` 替换为 MinIO 部署的 :ref:`alias <alias>`，用于获取 AD/LDAP 集成信息。

.. mc-cmd:: enable

   启用当前已配置的 AD/LDAP 提供方。

   .. tab-set::

      .. tab-item:: EXAMPLE

         以下示例在 ``myminio`` 部署上启用 AD/LDAP 配置。

         .. code-block:: shell
            :class: copyable

            mc admin idp ldap enable       \
                              myminio

      .. tab-item:: SYNTAX

         该命令语法如下：

         .. code-block:: shell
            :class: copyable

            mc [GLOBALFLAGS] admin idp ldap enable     \
                                            ALIAS

         - 将 ``ALIAS`` 替换为 MinIO 部署的 :ref:`alias <alias>`，用于启用 AD/LDAP 集成配置。

.. mc-cmd:: disable

   禁用当前已配置的 AD/LDAP 提供方。

   .. tab-set::

      .. tab-item:: EXAMPLE

         以下示例在 ``myminio`` 部署上禁用 AD/LDAP 配置。

         .. code-block:: shell
            :class: copyable

            mc admin idp ldap disable      \
                              myminio

      .. tab-item:: SYNTAX

         该命令语法如下：

         .. code-block:: shell
            :class: copyable

            mc [GLOBALFLAGS] admin idp ldap disable       \
                                            ALIAS

         - 将 ``ALIAS`` 替换为 MinIO 部署的 :ref:`alias <alias>`，用于禁用 AD/LDAP 集成配置。

全局标志
--------

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals
