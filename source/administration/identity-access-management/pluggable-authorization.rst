.. _minio-external-access-management-plugin:

=======================================
MinIO 外部访问管理插件
=======================================

.. default-domain:: minio


.. contents:: 目录
   :local:
   :depth: 1

概述
----

MinIO Access Management Plugin 提供了一个 ``REST`` 接口，可通过 Webhook 服务将授权流程卸载到外部系统。

启用后，MinIO 会将每次 API 调用的请求及凭证详情发送到已配置的外部 HTTP(S) 端点，并查找 ``ALLOW`` 或 ``DENY`` 响应。
因此，MinIO 可以将访问管理委托给外部系统，而不是依赖 S3 :ref:`基于策略的访问控制 <minio-policy>`。

配置设置
--------

你可以使用以下环境变量或配置设置来配置 MinIO 外部访问管理插件。

.. tab-set::

   .. tab-item:: 环境变量

      为部署中的每个 MinIO 服务器指定以下 :ref:`环境变量 <minio-server-envvar-external-access-management-plugin>`：

      .. code-block:: shell
         :class: copyable

         MINIO_POLICY_PLUGIN_URL="https://external-authz.example.net:8080/authz"

         # All other envvars are optional
         MINIO_POLICY_PLUGIN_AUTH_TOKEN="Bearer TOKEN"
         MINIO_POLICY_PLUGIN_ENABLE_HTTP2="OFF"
         MINIO_POLICY_PLUGIN_COMMENT="External Access Management using PROVIDER"

   .. tab-item:: 配置设置

      使用 :mc-cmd:`mc admin config set` 命令设置以下配置项：

      .. code-block:: shell
         :class: copyable

         mc admin config set policy_plugin \
            url="https://external-authz.example.net:8080/authz" \

            # All other config settings are optional
            auth_token="Bearer TOKEN" \
            enable_http2="off" \
            comment="External Access Management using PROVIDER"

认证与授权流程
--------------

应用程序的登录流程如下：

1. 客户端在执行 API 调用时携带认证信息

2. 已配置的身份管理器对客户端进行认证

3. MinIO 向已配置的访问管理插件 URL 发起 ``POST`` 调用，其中包含该 API 调用的上下文和认证数据

4. 授权成功时，访问管理器会返回 ``200 OK`` 响应，其 JSON 响应体格式为 ``result true`` 或 ``"result" : { "allow" : true }``：

如果访问管理器拒绝该授权请求，MinIO 会自动拦截并拒绝该 API 调用。

请求体示例
----------

以下 JSON 展示了发送到已配置访问管理器 Webhook 的 POST 请求体示例。

.. code-block:: json

   {
      "input": {
         "account": "minio",
         "groups": null,
         "action": "s3:ListBucket",
         "bucket": "test",
         "conditions": {
            "Authorization": [
            "AWS4-HMAC-SHA256 Credential=minio/20220507/us-east-1/s3/aws4_request, SignedHeaders=host;x-amz-content-sha256;x-amz-date, Signature=62012db6c47d697620cf6c68f0f45f6e34894589a53ab1faf6dc94338468c78a"
            ],
            "CurrentTime": [ "2022-05-07T18:31:41Z" ],
            "Delimiter": [ "/" ],
            "EpochTime": [
            "1651948301"
            ],
            "Prefix": [ "" ],
            "Referer": [ "" ],
            "SecureTransport": [ "false" ],
            "SourceIp": [ "127.0.0.1" ],
            "User-Agent": [ "MinIO (linux; amd64) minio-go/v7.0.24 mc/DEVELOPMENT.2022-04-20T23-07-53Z" ],
            "UserAgent": [ "MinIO (linux; amd64) minio-go/v7.0.24 mc/DEVELOPMENT.2022-04-20T23-07-53Z" ],
            "X-Amz-Content-Sha256": [ "e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855" ],
            "X-Amz-Date": [ "20220507T183141Z" ],
            "authType": [ "REST-HEADER" ],
            "principaltype": [ "Account" ],
            "signatureversion": [ "AWS4-HMAC-SHA256" ],
            "userid": [ "minio" ],
            "username": [ "minio" ],
            "versionid": [ "" ]
         },
         "owner": true,
         "object": "",
         "claims": {},
         "denyOnly": false
      }
   }

响应体示例
----------

MinIO 要求 Access Management 服务返回的响应体满足以下两种格式之一：

.. code-block:: json

   { "result" : true }

   { "result" : { "allow" : true } }
