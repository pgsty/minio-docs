.. _minio-external-identity-management-plugin:

=========================================
MinIO External Identity Management Plugin
=========================================

.. default-domain:: minio


.. contents:: 目录
   :local:
   :depth: 1

概述
----

MinIO Identity Management Plugin 提供了一个 REST 接口，用于通过 Webhook 服务将认证委托给外部身份管理器。

启用后，客户端应用程序使用 ``AssumeRoleWithCustomToken`` STS API 扩展为 MinIO 生成访问令牌。
MinIO 通过向已配置的插件端点发起 POST 请求来验证该令牌，并根据返回的响应判断客户端的认证状态。

配置设置
--------

你可以使用以下环境变量或配置设置来配置 MinIO Identity Management Plugin：

.. tab-set::

   .. tab-item:: Environment Variables

      为部署中的每个 MinIO 服务器指定以下 :ref:`environment variables <minio-server-envvar-external-identity-management-plugin>`：

      .. code-block:: shell
         :class: copyable

         MINIO_IDENTITY_PLUGIN_URL="https://external-auth.example.net:8080/auth"               
         MINIO_IDENTITY_PLUGIN_ROLE_POLICY="consoleAdmin"
         
         # All other envvars are optional
         MINIO_IDENTITY_PLUGIN_TOKEN="Bearer TOKEN"         
         MINIO_IDENTITY_PLUGIN_ROLE_ID="external-auth-provider"
         MINIO_IDENTITY_PLUGIN_COMMENT="External Identity Management using PROVIDER"

   .. tab-item:: Configuration Settings

      使用 :mc-cmd:`mc admin config set` 命令设置以下配置项：

      .. code-block:: shell
         :class: copyable

         mc admin config set identity_plugin \
            url="https://external-auth.example.net:8080/auth" \
            role_policy="consoleAdmin" \
            
            # All other config settings are optional
            token="Bearer TOKEN" \
            role_id="external-auth-provider" \
            comment="External Identity Management using PROVIDER"

认证与授权流程
--------------

应用程序的登录流程如下：

1. 使用 :ref:`minio-sts-assumerolewithcustomtoken` API 发起 POST 请求。

   该请求包含一个令牌，供已配置的外部身份管理器用于认证客户端。

2. MinIO 使用为 STS API 指定的令牌，向已配置的身份插件 URL 发起 POST 调用。

3. 认证成功后，身份管理器返回一个 ``200 OK`` 响应，其 ``content-type`` 为 ``application/json``，响应体结构如下：

   .. code-block:: json

      {
         "user": "<string>",
         "maxValiditySeconds": 3600,
         "claims": {"KEY": "VALUE", ...}
      }

   .. list-table::
      :stub-columns: 1
      :widths: 30 70
      :width: 100%

      * - ``user``
        - 所请求凭证的所有者

      * - ``maxValiditySeconds``
        - 返回凭证允许的最大过期时长

      * - ``claims``
        - 与所请求凭证关联的、由 ``"key": "value"`` 键值对组成的 claims JSON 字符串。
          如果存在 ``exp``、``parent`` 和 ``sub`` claims 对象，MinIO 会将其保留并忽略。

4. MinIO 向 STS API 请求返回响应，其中包含可用于发起已认证请求的临时凭证。

如果身份管理器拒绝该认证请求，或在处理过程中发生其他错误，则响应 *必须* 返回 ``403 FORBIDDEN`` HTTP 状态码，``content-type`` 为 ``application/json``，响应体结构如下：

.. code-block:: json

   {
   	"reason": "<string>"
   }

``"reason"`` 字段应包含返回 403 的原因。

创建与 Claims 匹配的策略
------------------------

使用 :mc:`mc admin policy` 命令创建与一个或多个 claim 值匹配的策略。
