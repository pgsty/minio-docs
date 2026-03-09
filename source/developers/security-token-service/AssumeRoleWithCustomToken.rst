.. _minio-sts-assumerolewithcustomtoken:

=============================
``AssumeRoleWithCustomToken``
=============================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

MinIO Security Token Service (STS) 的 ``AssumeRoleWithCustomToken`` API 端点会生成一个令牌，用于配合 :ref:`minio-external-identity-management-plugin` 使用。

请求端点
--------

``AssumeRoleWithCustomToken`` 端点的格式如下：

.. code-block:: shell

   POST https://minio.example.net?Action=AssumeRoleWithCustomToken[&ARGS]

以下示例使用了所有受支持的参数。
请将 ``minio.example.net`` 主机名替换为你的 MinIO 集群对应 URL：

.. code-block:: shell

   POST https://minio.example.net?Action=AssumeRoleWithCustomToken
   &Token=TOKEN
   &Version=2011-06-15
   &DurationSeconds=86000
   &RoleArn="external-auth-provider"

请求查询参数
~~~~~~~~~~~~

此端点支持以下查询参数：

.. list-table::
   :header-rows: 1
   :widths: 20 20 60
   :width: 100%

   * - 参数
     - 类型
     - 说明

   * - ``Token``
     - string
     - *必填*

       指定提交给外部身份管理器的 JSON Token。
       MinIO 期望身份管理器解析该令牌，并判断是否使用该令牌对客户端请求进行认证。

   * - ``Version``
     - string
     - *必填*

       指定 ``2011-06-15``。

   * - ``RoleArn``
     - string
     - *必填*

       指定与此 STS 请求关联的 Identity Manager Plugin 配置 ARN。

       更多信息请参见 :envvar:`MINIO_IDENTITY_PLUGIN_ROLE_ID` 或 :mc-conf:`identity_plugin role_id <identity_plugin.role_id>`。

       请注意，MinIO 在生成 RoleArn 时会自动为已配置的 ``ROLE_ID`` 添加前缀 ``idmp-``。
       如有需要，请在 ``ROLE_ID`` 中包含该字符串。

   * - ``DurationSeconds``
     - integer
     - *可选*
     
       指定临时凭证在多少秒后过期。
       默认值为 ``3600``。
       
       - 最小值为 ``900``，即 15 分钟。
       - 最大值为 ``604800``，即 7 天。

响应元素
--------

MinIO 返回一个 ``AssumeRoleWithCustomTokenResult`` 对象，其中 ``AssumedRoleUser.Credentials`` 对象包含 MinIO 生成的临时凭证：

- ``AccessKeyId`` - 应用程序用于认证的访问密钥。
- ``SecretKeyId`` - 应用程序用于认证的秘密密钥。
- ``Expiration`` - 凭证过期的 :rfc:`RFC3339 <3339>` 日期和时间。
- ``SessionToken`` - 应用程序用于认证的会话令牌。某些
  SDK 在使用临时凭证时可能要求此字段。

以下示例与 MinIO STS ``AssumeRoleWithCustomToken`` 端点返回的响应类似：

.. code-block:: xml

   <?xml version="1.0" encoding="UTF-8"?>
   <AssumeRoleWithCustomTokenResponse xmlns="https://sts.amazonaws.com/doc/2011-06-15/">
   <AssumeRoleWithCustomTokenResult>
      <Credentials>
         <AccessKeyId>ACCESS_KEY</AccessKeyId>
         <SecretAccessKey>SECRET_KEY</SecretAccessKey>
         <Expiration>YYYY-MM-DDTHH:MM:SSZ</Expiration>
         <SessionToken>TOKEN</SessionToken>
      </Credentials>
      <AssumedUser>custom:Alice</AssumedUser>
   </AssumeRoleWithCustomTokenResult>
   <ResponseMetadata>
      <RequestId>UNIQUE_ID</RequestId>
   </ResponseMetadata>
   </AssumeRoleWithCustomTokenResponse>

错误元素
--------

此 API 端点的 XML 错误响应与 AWS :aws-docs:`AssumeRoleWithWebIdentity response <STS/latest/APIReference/API_AssumeRoleWithWebIdentity.html#API_AssumeRoleWithWebIdentity_Errors>` 类似。
