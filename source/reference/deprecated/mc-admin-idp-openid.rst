.. _minio-mc-admin-idp-openid:

=======================
``mc admin idp openid``
=======================

.. default-domain:: minio

.. contents:: Table of Contents
   :local:
   :depth: 2

.. mc:: mc admin idp openid

.. versionchanged:: RELEASE.2023-05-26T23-31-54Z

   ``mc admin idp openid`` 及其子命令已由 :mc-cmd:`mc idp openid` 替代。

描述
----

.. start-mc-admin-idp-openid-desc

:mc-cmd:`mc admin idp openid` 命令允许你为第三方 :ref:`OpenID 身份与访问管理（IAM）集成 <minio-external-identity-management-openid>` 添加、修改、查看、列出、移除、启用和禁用服务器配置。

.. end-mc-admin-idp-openid-desc

在 :ref:`设置 OpenID 连接 <minio-external-identity-management-openid-configure>` 时，可通过定义配置设置来替代使用环境变量。


:mc-cmd:`mc admin idp openid` 命令包含以下子命令：

.. list-table::
   :header-rows: 1
   :widths: 40 60

   * - 子命令
     - 描述

   * - :mc-cmd:`mc admin idp openid add`
     - 创建 OpenID IDP 服务器配置。

   * - :mc-cmd:`mc admin idp openid update`
     - 修改现有的 OpenID IDP 服务器配置。

   * - :mc-cmd:`mc admin idp openid rm`
     - 从部署中移除 OpenID IDP 服务器配置。

   * - :mc-cmd:`mc admin idp openid ls`
     - 输出部署中现有 OpenID 服务器配置列表。

   * - :mc-cmd:`mc admin idp openid info`
     - 显示特定 OpenID 服务器配置的详细信息。

   * - :mc-cmd:`mc admin idp openid enable`
     - 启用 OpenID 服务器配置。

   * - :mc-cmd:`mc admin idp openid disable`
     - 禁用 OpenID 服务器配置。

配置参数
--------

:mc-cmd:`mc admin idp openid` 子命令支持配置参数。
这些参数定义了服务器与 IAM 提供方的交互方式。

有关配置参数的更详细说明，请参阅 :ref:`配置设置文档 <minio-open-id-config-settings>`。

语法
----

.. mc-cmd:: add

   为 OpenID 提供方创建一组新的配置。

   你可以多次运行该命令来设置多个 OpenID 提供方。

   添加多个 OpenID 提供方时，只能有一个是基于 JWT Claim 的提供方。
   其余都必须是基于角色的提供方。

   .. tab-set::

      .. tab-item:: 示例

         以下示例为 ``myminio`` 部署创建配置设置，在新的 ``test-config`` 配置中定义 Dex 集成。

         .. code-block:: shell
            :class: copyable

             mc admin idp openid add myminio test-config                                  \                                                
                client_id=minio-client-app                                                \
                client_secret=minio-client-app-secret                                     \           
                config_url="http://localhost:5556/dex/.well-known/openid-configuration"   \
                scopes="openid,groups"                                                    \
                redirect_uri="http://127.0.0.1:10000/oauth_callback"                      \
                role_policy="consoleAdmin"                                                           
                                    
      .. tab-item:: 语法

         命令语法如下：

         .. code-block:: shell
            :class: copyable

            mc [GLOBALFLAGS] admin idp openid add        \
                                       ALIAS             \
                                       [CFG_NAME]        \
                                       [CFG_PARAM1]      \
                                       [CFG_PARAM2]...

         - 将 ``ALIAS`` 替换为用于配置 OpenID 集成的 MinIO 部署的 :ref:`alias <alias>`。
         - 将 ``CFG_NAME`` 替换为该配置的唯一字符串。
           如果未指定，命令会创建默认配置值。
         - 将 ``[CFG_PARAM#]`` 替换为各个 :ref:`配置设置 <minio-open-id-config-settings>` 键值对，格式为 ``PARAMETER="value"``。

.. mc-cmd:: update

   修改 OpenID 提供方的现有配置集合。

   .. tab-set::

      .. tab-item:: 示例

         以下示例修改了 ``myminio`` 部署中 ``test-config``（用于 Dex 集成）的两个配置项。

         .. code-block:: shell
            :class: copyable

            mc admin idp openid update                      \
                                myminio                     \
                                test_config                 \
                                scopes="openid,groups"      \
                                role_policy="consoleAdmin"                                                              
                                    
      .. tab-item:: 语法

         命令语法如下：

         .. code-block:: shell
            :class: copyable

            mc [GLOBALFLAGS] admin idp openid update           \
                                              ALIAS            \
                                              [CFG_NAME]       \
                                              [CFG_PARAM1]     \
                                              [CFG_PARAM2]...

         - 将 ``ALIAS`` 替换为用于配置 OpenID 集成的 MinIO 部署的 :ref:`alias <alias>`。
         - 将 ``CFG_NAME`` 替换为该配置的唯一字符串。
           如果未指定，命令会更新默认配置。
         - 将 ``[CFG_PARAM#]`` 替换为要更新的各个 :ref:`配置设置 <minio-open-id-config-settings>` 键值对，格式为 ``PARAMETER="value"``。

.. mc-cmd:: rm, remove

   删除 OpenID 提供方的现有配置集合。

   .. tab-set::

      .. tab-item:: 示例

         以下示例删除 ``myminio`` 部署中的 ``test-config`` 配置。

         .. code-block:: shell
            :class: copyable

            mc admin idp openid rm myminio test_config                                                              
                                    
      .. tab-item:: 语法

         命令语法如下：

         .. code-block:: shell
            :class: copyable

            mc [GLOBALFLAGS] admin idp openid rm     \
                                              ALIAS      \
                                              [CFG_NAME]

         - 将 ``ALIAS`` 替换为用于配置 OpenID 集成的 MinIO 部署的 :ref:`alias <alias>`。
         - 将 ``CFG_NAME`` 替换为该配置的唯一字符串。
           如果未指定，命令会删除默认配置。

.. mc-cmd:: ls, list

   输出 OpenID 提供方现有配置集合列表。

   .. tab-set::

      .. tab-item:: 示例

         以下示例输出 ``myminio`` 部署定义的所有 OpenID 配置集合列表。

         .. code-block:: shell
            :class: copyable

            mc admin idp openid ls myminio                                                            
                                    
      .. tab-item:: 语法

         命令语法如下：

         .. code-block:: shell
            :class: copyable

            mc [GLOBALFLAGS] admin idp openid ls ALIAS

         - 将 ``ALIAS`` 替换为要列出 OpenID 集成的 MinIO 部署的 :ref:`alias <alias>`。


.. mc-cmd:: info

   输出 OpenID 提供方现有服务器配置集合中定义的参数值。

   .. tab-set::

      .. tab-item:: 示例

         以下示例输出 ``myminio`` 部署中 ``test_config`` 这组 OpenID 设置所定义的配置项。

         .. code-block:: shell
            :class: copyable

            mc admin idp openid info myminio test_config
                                    
      .. tab-item:: 语法

         命令语法如下：

         .. code-block:: shell
            :class: copyable

            mc [GLOBALFLAGS] admin idp openid info     \
                                              ALIAS      \
                                              [CFG_NAME]

         - 将 ``ALIAS`` 替换为用于配置 OpenID 集成的 MinIO 部署的 :ref:`alias <alias>`。
         - 将 ``CFG_NAME`` 替换为该配置的唯一字符串。
           如果未指定，则显示默认服务器配置的信息。

.. mc-cmd:: enable

   开始使用 OpenID 提供方的现有配置集合。

   .. tab-set::

      .. tab-item:: 示例

         以下示例启用 ``myminio`` 部署上定义为 ``test_config`` 的服务器配置。

         .. code-block:: shell
            :class: copyable

            mc admin idp openid enable       \
                                myminio      \
                                test_config

      .. tab-item:: 语法

         命令语法如下：

         .. code-block:: shell
            :class: copyable

            mc [GLOBALFLAGS] admin idp openid enable     \
                                              ALIAS      \
                                              [CFG_NAME]

         - 将 ``ALIAS`` 替换为用于配置 OpenID 集成的 MinIO 部署的 :ref:`alias <alias>`。
         - 将 ``CFG_NAME`` 替换为该配置的唯一字符串。
           如果未指定，命令会启用默认配置值。

.. mc-cmd:: disable

   停止使用 OpenID 提供方的一组配置。

   .. tab-set::

      .. tab-item:: 示例

         以下示例禁用 ``myminio`` 部署上定义为 ``test_config`` 的服务器配置。

         .. code-block:: shell
            :class: copyable

            mc admin idp openid disable      \
                                myminio      \
                                test_config

      .. tab-item:: 语法

         命令语法如下：

         .. code-block:: shell
            :class: copyable

            mc [GLOBALFLAGS] admin idp openid disable       \
                                              ALIAS         \
                                              [CFG_NAME]

         - 将 ``ALIAS`` 替换为用于配置 OpenID 集成的 MinIO 部署的 :ref:`alias <alias>`。
         - 将 ``CFG_NAME`` 替换为该配置的唯一字符串。
           如果未指定，命令会禁用默认配置值。



全局标志
--------

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals
