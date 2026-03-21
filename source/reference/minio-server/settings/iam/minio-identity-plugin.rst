.. _minio-server-envvar-external-identity-management-plugin:

=========================================
MinIO 身份管理插件设置
=========================================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

本页介绍如何通过 MinIO Identity Management Plugin 启用外部身份管理的相关设置。
有关如何使用这些设置的教程，请参阅 :ref:`minio-external-identity-management-plugin`。

.. include:: /includes/common-mc-admin-config.rst
   :start-after: start-minio-settings-defined
   :end-before: end-minio-settings-defined

.. include:: /includes/common-mc-admin-config.rst
   :start-after: start-minio-settings-test-before-prod
   :end-before: end-minio-settings-test-before-prod

示例
----

配置 MinIO Identity Management Plugin 时，至少必须定义所有*必需*设置。
以下示例展示了最小必需配置。

.. tab-set::
   
   .. tab-item:: 环境变量
      :sync: envvar

      .. code-block:: shell
   
         MINIO_IDENTITY_PLUGIN_URL="https://authservice.example.net:8080/auth"
         MINIO_IDENTITY_PLUGIN_ROLE_POLICY="ConsoleUser"

   .. tab-item:: 配置设置
      :sync: config

      .. mc-conf:: identity_plugin

      使用 :mc:`mc admin config set` 创建或更新身份管理插件配置。
      ``identity_plugin url`` 参数为必填项。
      其他可选参数以空白字符（" "）分隔的列表形式指定。

      .. code-block:: shell

         mc admin config set identity_plugin                  \
            url="https://external-auth.example.net:8080/auth" \
            role_policy="consoleAdmin"                        \
            [ARGUMENT=VALUE] ... 

设置
----

URL
~~~

*必需*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_IDENTITY_PLUGIN_URL
   
   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: identity_plugin url
         :delimiter: " "
      
.. include:: /includes/common-minio-external-auth.rst
   :start-after: start-minio-identity-management-plugin-url
   :end-before: end-minio-identity-management-plugin-url

角色策略
~~~~~~~~

*必需*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_IDENTITY_PLUGIN_ROLE_POLICY

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: identity_plugin role_policy
         :delimiter: " "
   
.. include:: /includes/common-minio-external-auth.rst
   :start-after: start-minio-identity-management-role-policy
   :end-before: end-minio-identity-management-role-policy

启用
~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      
      此设置不提供环境变量选项。

   .. tab-item:: 配置项
      :selected:

      .. mc-conf:: identity_plugin enabled
         :delimiter: " "

设置为 ``false`` 以禁用身份提供方配置。

如果设置为 ``false``，应用程序将无法生成 STS 凭证，也无法通过已配置的提供方对 MinIO 进行身份验证。

默认为 ``true`` 或 "enabled"。

令牌
~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_IDENTITY_PLUGIN_TOKEN

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: identity_plugin token
         :delimiter: " "

.. include:: /includes/common-minio-external-auth.rst
   :start-after: start-minio-identity-management-auth-token
   :end-before: end-minio-identity-management-auth-token

角色 ID
~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_IDENTITY_PLUGIN_ROLE_ID

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: identity_plugin role_id
         :delimiter: " "

.. include:: /includes/common-minio-external-auth.rst
   :start-after: start-minio-identity-management-role-id
   :end-before: end-minio-identity-management-role-id

注释
~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_IDENTITY_PLUGIN_COMMENT

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: identity_plugin comment
         :delimiter: " "

.. include:: /includes/common-minio-external-auth.rst
   :start-after: start-minio-identity-management-comment
   :end-before: end-minio-identity-management-comment
