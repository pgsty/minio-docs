.. start-minio-oidc-policy-variables

下表列出了用于授权 :ref:`OIDC 管理用户 <minio-external-identity-management-openid>`
的受支持策略变量。

每个变量都对应认证用户 JWT token 中返回的一项 claim：

.. list-table::
   :header-rows: 1
   :widths: 40 60
   :width: 100%

   * - 变量
     - 说明

   * - ``jwt:sub``
     - 返回用户的 ``sub`` claim。

   * - ``jwt:iss``
     - 返回 ID token 中的 Issuer Identifier claim。

   * - ``jwt:aud``
     - 返回 ID token 中的 Audience claim。

   * - ``jwt:jti``
     - 返回客户端认证信息中的 JWT ID claim。

   * - ``jwt:upn``
     - 返回客户端认证信息中的 User Principal Name claim。

   * - ``jwt:name``
     - 返回用户的 ``name`` claim。

   * - ``jwt:groups``
     - 返回用户的 ``groups`` claim。

   * - ``jwt:given_name``
     - 返回用户的 ``given_name`` claim。

   * - ``jwt:family_name``
     - 返回用户的 ``family_name`` claim。

   * - ``jwt:middle_name``
     - 返回用户的 ``middle_name`` claim。

   * - ``jwt:nickname``
     - 返回用户的 ``nickname`` claim。

   * - ``jwt:preferred_username``
     - 返回用户的 ``preferred_username`` claim。

   * - ``jwt:profile``
     - 返回用户的 ``profile`` claim。

   * - ``jwt:picture``
     - 返回用户的 ``picture`` claim。

   * - ``jwt:website``
     - 返回用户的 ``website`` claim。

   * - ``jwt:email``
     - 返回用户的 ``email`` claim。

   * - ``jwt:gender``
     - 返回用户的 ``gender`` claim。

   * - ``jwt:birthdate``
     - 返回用户的 ``birthdate`` claim。

   * - ``jwt:phone_number``
     - 返回用户的 ``phone_number`` claim。

   * - ``jwt:address``
     - 返回用户的 ``address`` claim。

   * - ``jwt:scope``
     - 返回用户的 ``scope`` claim。

   * - ``jwt:client_id``
     - 返回用户的 ``client_id`` claim。

关于这些 scope 的更多信息，请参阅
`OpenID Connect Core 1.0 <https://openid.net/specs/openid-connect-core-1_0.html>`__ 文档。
你所选的 OIDC 提供方也可能有更具体的补充文档。

例如，以下策略使用变量将认证用户的 ``preferred_username`` 替换到 ``Resource`` 字段中，
使该用户只能访问与其用户名匹配的前缀：

.. code-block:: json

   {
   "Version": "2012-10-17",
   "Statement": [
         {
            "Action": ["s3:ListBucket"],
            "Effect": "Allow",
            "Resource": ["arn:aws:s3:::mybucket"],
            "Condition": {"StringLike": {"s3:prefix": ["${jwt:preferred_username}/*"]}}
         },
         {
            "Action": [
            "s3:GetObject",
            "s3:PutObject"
            ],
            "Effect": "Allow",
            "Resource": ["arn:aws:s3:::mybucket/${jwt:preferred_username}/*"]
         }
      ]
   }

MinIO 会将 ``Resource`` 字段中的 ``${jwt:preferred_username}`` 变量，
替换为 JWT token 中 ``preferred_username`` 的值。
随后，MinIO 会评估该策略，并对请求的 API 和资源授予或撤销访问权限。

.. end-minio-oidc-policy-variables
