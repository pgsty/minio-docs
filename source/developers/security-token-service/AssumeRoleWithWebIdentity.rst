.. _minio-sts-assumerolewithwebidentity:

=============================
``AssumeRoleWithWebIdentity``
=============================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

MinIO Security Token Service (STS) ``AssumeRoleWithWebIdentity`` API
端点使用由
:ref:`已配置的 OpenID Identity Provider (IDP)
<minio-external-identity-management-openid-configure>`
返回的 JSON Web Token (JWT) 生成临时访问凭证。本文档说明 MinIO
服务器的 ``AssumeRoleWithWebIdentity`` 端点。关于如何使用 S3 兼容 SDK
实现 STS，请参阅对应 SDK 的文档。

MinIO STS ``AssumeRoleWithWebIdentity`` API 端点参考了
AWS :aws-docs:`AssumeRoleWithWebIdentity
<STS/latest/APIReference/API_AssumeRoleWithWebIdentity.html>`
端点，并共享部分请求/响应元素。本文档说明 MinIO 特有语法，并链接到 AWS 参考文档以获取
所有共享元素的说明。

请求端点
--------

``AssumeRoleWithWebIdentity`` 端点格式如下：

.. code-block:: shell

   POST https://minio.example.net?Action=AssumeRoleWithWebIdentity[&ARGS]

以下示例使用了所有受支持参数。请将
``minio.example.net`` 主机名替换为你的 MinIO
集群对应 URL：

.. code-block:: shell

   POST https://minio.example.net?Action=AssumeRoleWithWebIdentity
   &WebIdentityToken=TOKEN
   &Version=2011-06-15
   &DurationSeconds=86000
   &Policy={}

.. _minio-assumerolewithwebidentity-query-parameters:

请求查询参数
~~~~~~~~~~~~

该端点支持以下查询参数：

.. list-table::
   :header-rows: 1
   :widths: 20 20 60
   :width: 100%

   * - 参数
     - 类型
     - 说明

   * - ``WebIdentityToken``
     - string
     - *必填*

       指定由
       :ref:`已配置的 OpenID Identity Provider
       <minio-external-identity-management-openid-configure>`
       返回的 JSON Web Token (JWT)。

   * - ``Version``
     - string
     - *必填*

       指定 ``2011-06-15``。

   * - ``DurationSeconds``
     - integer
     - *可选*

       指定临时凭证过期前的秒数。
       默认为 ``3600``。

       - 最小值为 ``900``，即 15 分钟。
       - 最大值为 ``604800``，即 7 天。

       如果省略 ``DurationSeconds``，MinIO 在使用默认时长前会先检查 JWT token 中的
       ``exp`` claim。有关 JSON Web Token 过期时间的更多信息，请参见
       `RFC 7519 4.1.4: Expiration Time Claim
       <https://datatracker.ietf.org/doc/html/rfc7519#section-4.1.4>`__。

   * - ``Policy``
     - string
     - *可选*

       指定 URL 编码、JSON 格式的 :ref:`policy <minio-policy>`，
       作为内联会话策略使用。

       - 最小字符串长度为 ``1``。
       - 最大字符串长度为 ``2048``。

       临时凭证的最终权限是 :ref:`JWT claim
       <minio-external-identity-management-openid-access-control>`
       中指定策略与所给内联策略的交集。应用只能执行那些已被显式授权的操作。

       内联策略可以指定 JWT claim 策略所允许权限的子集。
       应用绝不会获得超出 JWT claim 策略所指定范围的权限。

       省略该参数则仅使用 JWT claim 策略。

       有关 MinIO 认证与授权的更多信息，请参见 :ref:`minio-access-management`。

   * - ``RoleArn``
     - string
     - *可选*

       用于所有用户认证请求的角色 Amazon Resource Number (ARN)。
       如果使用该参数，必须通过 ``role_policy`` 配置参数或 ``MINIO_IDENTITY_OPENID_ROLE_POLICY`` 环境变量，
       为 RoleArn 对应的 provider 定义匹配的 OIDC RolePolicy。

       使用时，所有有效的授权请求都会假定同一组由 RolePolicy 提供的权限。
       你可以使用 :ref:`OpenID Policy Variables <minio-policy-variables-oidc>` 创建策略，
       以编程方式管理每个用户可访问的内容。

       如果未提供 RoleArn，MinIO 会尝试通过基于 JWT 的 claim 进行授权。

响应元素
--------

该 API 端点的 XML 响应与 AWS
:aws-docs:`AssumeRoleWithWebIdentity response
<STS/latest/APIReference/API_AssumeRoleWithWebIdentity.html#API_AssumeRoleWithWebIdentity_ResponseElements>`
类似。具体而言，MinIO 返回 ``AssumeRoleWithWebIdentityResult`` 对象，
其中 ``AssumedRoleUser.Credentials`` 对象包含 MinIO 生成的临时
凭证：

- ``AccessKeyId`` - 应用用于认证的访问密钥。
- ``SecretKeyId`` - 应用用于认证的秘密密钥。
- ``Expiration`` - 凭证过期的 :rfc:`RFC3339 <3339>` 日期和时间。
- ``SessionToken`` - 应用用于认证的会话 token。某些
  SDK 在使用临时凭证时可能需要此字段。

以下示例与 MinIO STS
``AssumeRoleWithWebIdentity`` 端点返回的响应类似：

.. code-block:: xml

   <?xml version="1.0" encoding="UTF-8"?>
   <AssumeRoleWithWebIdentityResponse xmlns="https://sts.amazonaws.com/doc/2011-06-15/">
   <AssumeRoleWithWebIdentityResult>
      <AssumedRoleUser>
         <Arn/>
         <AssumeRoleId/>
      </AssumedRoleUser>
      <Credentials>
         <AccessKeyId>Y4RJU1RNFGK48LGO9I2S</AccessKeyId>
         <SecretAccessKey>sYLRKS1Z7hSjluf6gEbb9066hnx315wHTiACPAjg</SecretAccessKey>
         <Expiration>2019-08-08T20:26:12Z</Expiration>
         <SessionToken>eyJhbGciOiJIUzUxMiIsInR5cCI6IkpXVCJ9.eyJhY2Nlc3NLZXkiOiJZNFJKVTFSTkZHSzQ4TEdPOUkyUyIsImF1ZCI6IlBvRWdYUDZ1Vk80NUlzRU5SbmdEWGo1QXU1WWEiLCJhenAiOiJQb0VnWFA2dVZPNDVJc0VOUm5nRFhqNUF1NVlhIiwiZXhwIjoxNTQxODExMDcxLCJpYXQiOjE1NDE4MDc0NzEsImlzcyI6Imh0dHBzOi8vbG9jYWxob3N0Ojk0NDMvb2F1dGgyL3Rva2VuIiwianRpIjoiYTBiMjc2MjktZWUxYS00M2JmLTg3MzktZjMzNzRhNGNkYmMwIn0.ewHqKVFTaP-j_kgZrcOEKroNUjk10GEp8bqQjxBbYVovV0nHO985VnRESFbcT6XMDDKHZiWqN2vi_ETX_u3Q-w</SessionToken>
      </Credentials>
   </AssumeRoleWithWebIdentityResult>
   <ResponseMetadata/>
   </AssumeRoleWithWebIdentityResponse>

错误元素
--------

该 API 端点的 XML 错误响应与 AWS
:aws-docs:`AssumeRoleWithWebIdentity response
<STS/latest/APIReference/API_AssumeRoleWithWebIdentity.html#API_AssumeRoleWithWebIdentity_Errors>`
类似。
