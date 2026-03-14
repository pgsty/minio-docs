.. _minio-server-envvar-external-access-management-plugin:

=======================================
MinIO 访问管理插件设置
=======================================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

本页说明如何配置 MinIO Access Management Plugin 以启用外部授权管理。
有关这些设置的使用教程，请参见 :ref:`minio-external-access-management-plugin`。

.. include:: /includes/common-mc-admin-config.rst
   :start-after: start-minio-settings-defined
   :end-before: end-minio-settings-defined

.. include:: /includes/common-mc-admin-config.rst
   :start-after: start-minio-settings-test-before-prod
   :end-before: end-minio-settings-test-before-prod

示例
----

在设置 MinIO Access Management 插件时，至少必须定义所有 *Required* 设置。
此处示例展示了最小必需配置。

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. code-block:: shell

         MINIO_POLICY_PLUGIN_URL="https://authzservice.example.net:8080/authz"

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: policy_plugin

      使用 :mc:`mc admin config set` 命令创建或更新访问管理插件配置。
      ``policy_plugin url`` 参数为必填。
      其他可选参数请以空白字符（" "）分隔的列表形式指定。
      

      .. code-block:: shell

         mc admin config set policy_plugin                     \
            url="https://authzservice.example.net:8080/authz"  \
            [ARGUMENT=VALUE] ...

设置
----

URL
~~~

*必需*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_POLICY_PLUGIN_URL

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: policy_plugin url
         :delimiter: " "

.. include:: /includes/common-minio-external-auth.rst
   :start-after: start-minio-access-management-plugin-url
   :end-before: end-minio-access-management-plugin-url

认证令牌
~~~~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_POLICY_PLUGIN_AUTH_TOKEN

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: policy_plugin auth_token
         :delimiter: " "

.. include:: /includes/common-minio-external-auth.rst
   :start-after: start-minio-access-management-plugin-auth-token
   :end-before: end-minio-access-management-plugin-auth-token

HTTP2
~~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_POLICY_PLUGIN_ENABLE_HTTP2

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: policy_plugin enable_http2
         :delimiter: " "

.. include:: /includes/common-minio-external-auth.rst
   :start-after: start-minio-access-management-plugin-enable-http2
   :end-before: end-minio-access-management-plugin-enable-http2

注释
~~~~

*可选*

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_POLICY_PLUGIN_COMMENT

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: policy_plugin comment
         :delimiter: " "

.. include:: /includes/common-minio-external-auth.rst
   :start-after: start-minio-access-management-plugin-comment
   :end-before: end-minio-access-management-plugin-comment
