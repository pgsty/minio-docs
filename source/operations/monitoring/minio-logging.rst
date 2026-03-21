.. _minio-logging:

========================================
将服务日志或审计日志发布到外部服务
========================================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 1

MinIO 会将所有 :mc:`minio server` 操作输出到系统控制台。
如何读取这些日志取决于 server 进程的管理方式。
例如，如果 server 通过 ``systemd`` 脚本进行管理，
你可以使用 ``journalctl -u SERVICENAME.service`` 读取日志。
请将 ``SERVICENAME`` 替换为 MinIO 服务名称。

MinIO 还支持将服务日志和审计日志发布到 HTTP Webhook。

- :ref:`服务日志 <minio-logging-publish-server-logs>` 包含与系统控制台中相同的
  :mc:`minio server` 操作日志。服务日志适用于常规监控与运维排障。

- :ref:`审计日志 <minio-logging-publish-audit-logs>` 会以更细粒度描述
  MinIO 部署上的每一次操作。审计日志适用于要求对操作进行详细追踪的
  安全标准与合规规范。

MinIO 会将日志作为 JSON 文档，通过 ``PUT`` 请求发送到每个已配置端点。
端点服务器负责处理这些 JSON 文档。
MinIO 要求显式配置每个 Webhook 端点，默认情况下 *不会* 向 Webhook 发布日志。

.. _minio-logging-publish-server-logs:

将服务日志发布到 HTTP Webhook
----------------------------------------

你可以通过环境变量 *或* 运行时配置项，配置一个新的 HTTP Webhook 端点，
让 MinIO 将 :mc:`minio server` 日志发布到该端点。

.. tab-set::

   .. tab-item:: 环境变量

      MinIO 支持使用 :ref:`环境变量 <minio-server-envvar-logging-regular>`
      指定 :mc:`minio server` 日志 HTTP Webhook 端点及其相关配置项。

      下面的示例代码设置了配置日志 HTTP Webhook 端点所需的 *全部* 环境变量。
      其中最少 *必须* 配置的变量为：

      - :envvar:`MINIO_LOGGER_WEBHOOK_ENABLE`
      - :envvar:`MINIO_LOGGER_WEBHOOK_ENDPOINT`

      .. cond:: windows
      
         .. code-block:: shell
            :class: copyable
         
               set MINIO_LOGGER_WEBHOOK_ENABLE_<IDENTIFIER>="on"
               set MINIO_LOGGER_WEBHOOK_ENDPOINT_<IDENTIFIER>="https://webhook-1.example.net"
               set MINIO_LOGGER_WEBHOOK_AUTH_TOKEN_<IDENTIFIER>="TOKEN"

      .. cond:: not windows

         .. code-block:: shell
            :class: copyable

               export MINIO_LOGGER_WEBHOOK_ENABLE_<IDENTIFIER>="on"
               export MINIO_LOGGER_WEBHOOK_ENDPOINT_<IDENTIFIER>="https://webhook-1.example.net"
               export MINIO_LOGGER_WEBHOOK_AUTH_TOKEN_<IDENTIFIER>="TOKEN"

      - 将 ``<IDENTIFIER>`` 替换为该 HTTP Webhook 端点的唯一描述字符串。
        与新日志 HTTP Webhook 相关的所有环境变量都应使用同一个 ``<IDENTIFIER>``。

        如果指定的 ``<IDENTIFIER>`` 与现有日志端点匹配，
        新设置将 *覆盖* 该端点的现有设置。
        可使用 :mc-cmd:`mc admin config get logger_webhook <mc admin config get>`
        查看当前已配置的日志 HTTP Webhook 端点。

      - 将 ``https://webhook-1.example.net`` 替换为 HTTP Webhook 端点的 URL。

      - 将 ``TOKEN`` 替换为适用于该端点的认证令牌类型。
        对于无需认证的端点，可省略该项。

        为支持多种令牌类型，MinIO 会按 *原样* 使用该值创建请求认证头。
        根据端点不同，你可能需要包含额外信息。

        例如，对于 Bearer token，请在前面加上 ``Bearer``：

        .. cond:: windows
        
           .. code-block:: shell

              set MINIO_LOGGER_WEBHOOK_AUTH_TOKEN_myendpoint="Bearer 1a2b3c4f5e"

        .. cond:: not windows
        
           .. code-block:: shell

              export MINIO_LOGGER_WEBHOOK_AUTH_TOKEN_myendpoint="Bearer 1a2b3c4f5e"

        请根据端点要求调整该值。
        自定义认证格式可能类似如下：

        .. cond:: windows
        
           .. code-block:: shell

              set MINIO_LOGGER_WEBHOOK_AUTH_TOKEN_xyz="ServiceXYZ 1a2b3c4f5e"
        
        .. cond:: not windows
        
           .. code-block:: shell

              export MINIO_LOGGER_WEBHOOK_AUTH_TOKEN_xyz="ServiceXYZ 1a2b3c4f5e"

        详情请参阅目标服务的文档。

      重启 MinIO server 以应用新的配置项。
      你必须在部署中的 *所有* MinIO server 上指定相同的环境变量和设置。

   .. tab-item:: 配置项

      MinIO 支持在 MinIO 部署上使用 :mc-cmd:`mc admin config set` 命令和
      :mc-conf:`logger_webhook` 配置键新增或更新日志 HTTP Webhook 端点。
      应用任何新增或更新后的配置项都需要重启 MinIO 部署。

      下面的示例代码设置了配置日志 HTTP Webhook 端点相关的 *全部* 配置项。
      其中最少 *必须* 配置的项为
      :mc-conf:`logger_webhook endpoint <logger_webhook.endpoint>`：

      .. code-block:: shell
         :class: copyable

         mc admin config set ALIAS/ logger_webhook:IDENTIFIER  \
            endpoint="https://webhook-1.example.net"           \
            auth_token="TOKEN" 

      - 将 ``<IDENTIFIER>`` 替换为该 HTTP Webhook 端点的唯一描述字符串。
        与新日志 HTTP Webhook 相关的所有环境变量都应使用同一个 ``<IDENTIFIER>``。

        如果指定的 ``<IDENTIFIER>`` 与现有日志端点匹配，
        新设置将 *覆盖* 该端点的现有设置。
        可使用 :mc-cmd:`mc admin config get logger_webhook <mc admin config get>`
        查看当前已配置的日志 HTTP Webhook 端点。

      - 将 ``https://webhook-1.example.net`` 替换为 HTTP Webhook 端点的 URL。

      - 将 ``TOKEN`` 替换为适用于该端点的认证令牌类型。
        对于无需认证的端点，可省略该项。

        为支持多种令牌类型，MinIO 会按 *原样* 使用该值创建请求认证头。
        根据端点不同，你可能需要包含额外信息。

        例如，对于 Bearer token，请在前面加上 ``Bearer``：

        .. code-block:: shell
           :class: copyable

            mc admin config set ALIAS/ logger_webhook    \
               endpoint="https://webhook-1.example.net"  \
               auth_token="Bearer 1a2b3c4f5e"

        请根据端点要求调整该值。
        自定义认证格式可能类似如下：

        .. code-block:: shell
           :class: copyable

           mc admin config set ALIAS/ logger_webhook    \
              endpoint="https://webhook-1.example.net"  \
              auth_token="ServiceXYZ 1a2b3c4f5e"

        详情请参阅目标服务的文档。

.. _minio-logging-publish-audit-logs:

将审计日志发布到 HTTP Webhook
----------------------------------------

你可以通过环境变量 *或* 运行时配置项，配置一个新的 HTTP Webhook 端点，
让 MinIO 将审计日志发布到该端点：

.. tab-set::

   .. tab-item:: 环境变量

      MinIO 支持使用 :ref:`环境变量 <minio-server-envvar-logging-audit>`
      指定审计日志 HTTP Webhook 端点及其相关配置项。

      下面的示例代码设置了配置审计日志 HTTP Webhook 端点所需的 *全部* 环境变量。
      其中最少 *必须* 配置的变量为：

      - :envvar:`MINIO_AUDIT_WEBHOOK_ENABLE`
      - :envvar:`MINIO_AUDIT_WEBHOOK_ENDPOINT`

      .. cond:: windows
      
         .. code-block:: shell
            :class: copyable

            set MINIO_AUDIT_WEBHOOK_ENABLE_<IDENTIFIER>="on"
            set MINIO_AUDIT_WEBHOOK_ENDPOINT_<IDENTIFIER>="https://webhook-1.example.net"
            set MINIO_AUDIT_WEBHOOK_AUTH_TOKEN_<IDENTIFIER>="TOKEN"
            set MINIO_AUDIT_WEBHOOK_CLIENT_CERT_<IDENTIFIER>="cert.pem"
            set MINIO_AUDIT_WEBHOOK_CLIENT_KEY_<IDENTIFIER>="cert.key"

      .. cond:: not windows
      
         .. code-block:: shell
            :class: copyable

            export MINIO_AUDIT_WEBHOOK_ENABLE_<IDENTIFIER>="on"
            export MINIO_AUDIT_WEBHOOK_ENDPOINT_<IDENTIFIER>="https://webhook-1.example.net"
            export MINIO_AUDIT_WEBHOOK_AUTH_TOKEN_<IDENTIFIER>="TOKEN"
            export MINIO_AUDIT_WEBHOOK_CLIENT_CERT_<IDENTIFIER>="cert.pem"
            export MINIO_AUDIT_WEBHOOK_CLIENT_KEY_<IDENTIFIER>="cert.key"

      - 将 ``<IDENTIFIER>`` 替换为该 HTTP Webhook 端点的唯一描述字符串。
        与新审计日志 HTTP Webhook 相关的所有环境变量都应使用同一个 ``<IDENTIFIER>``。

        如果指定的 ``<IDENTIFIER>`` 与现有日志端点匹配，
        新设置将 *覆盖* 该端点的现有设置。
        可使用 :mc-cmd:`mc admin config get audit_webhook <mc admin config get>`
        查看当前已配置的审计日志 HTTP Webhook 端点。

      - 将 ``https://webhook-1.example.net`` 替换为 HTTP Webhook 端点的 URL。

      - 将 ``TOKEN`` 替换为适用于该端点的认证令牌类型。
        对于无需认证的端点，可省略该项。

        为支持多种令牌类型，MinIO 会按 *原样* 使用该值创建请求认证头。
        根据端点不同，你可能需要包含额外信息。

        例如，对于 Bearer token，请在前面加上 ``Bearer``：

        .. cond:: windows
        
           .. code-block:: shell

              set MINIO_AUDIT_WEBHOOK_AUTH_TOKEN_myendpoint="Bearer 1a2b3c4f5e"

        .. cond:: not windows
        
           .. code-block:: shell

              export MINIO_AUDIT_WEBHOOK_AUTH_TOKEN_myendpoint="Bearer 1a2b3c4f5e"

        请根据端点要求调整该值。
        自定义认证格式可能类似如下：

        .. cond:: windows
        
           .. code-block:: shell

              set MINIO_AUDIT_WEBHOOK_AUTH_TOKEN_xyz="ServiceXYZ 1a2b3c4f5e"

        .. cond:: not windows
        
           .. code-block:: shell

              export MINIO_AUDIT_WEBHOOK_AUTH_TOKEN_xyz="ServiceXYZ 1a2b3c4f5e"

        详情请参阅目标服务的文档。

      - 将 ``cert.pem`` 和 ``cert.key`` 替换为要向 HTTP Webhook server 提交的
        x.509 TLS 证书公钥与私钥。
        对于不要求客户端出示 TLS 证书的端点，可省略该项。

      重启 MinIO server 以应用新的配置项。
      你必须在部署中的 *所有* MinIO server 上指定相同的环境变量和设置。

   .. tab-item:: 配置项

      MinIO 支持在 MinIO 部署上使用 :mc-cmd:`mc admin config set` 命令和
      :mc-conf:`audit_webhook` 配置键新增或更新审计日志 HTTP Webhook 端点。
      应用任何新增或更新后的配置项都需要重启 MinIO 部署。

      下面的示例代码设置了配置审计日志 HTTP Webhook 端点相关的 *全部* 配置项。
      其中最少 *必须* 配置的项为
      :mc-conf:`audit_webhook endpoint <audit_webhook.endpoint>`：

      .. code-block:: shell
         :class: copyable

         mc admin config set ALIAS/ audit_webhook:IDENTIFIER  \
            endpoint="https://webhook-1.example.net"          \
            auth_token="TOKEN"                                \
            client_cert="cert.pem"                            \
            client_key="cert.key"

      - 将 ``<IDENTIFIER>`` 替换为该 HTTP Webhook 端点的唯一描述字符串。
        与新审计日志 HTTP Webhook 相关的所有环境变量都应使用同一个 ``<IDENTIFIER>``。

        如果指定的 ``<IDENTIFIER>`` 与现有日志端点匹配，
        新设置将 *覆盖* 该端点的现有设置。
        可使用 :mc-cmd:`mc admin config get audit_webhook <mc admin config get>`
        查看当前已配置的审计日志 HTTP Webhook 端点。

      - 将 ``https://webhook-1.example.net`` 替换为 HTTP Webhook 端点的 URL。

      - 将 ``TOKEN`` 替换为适用于该端点的认证令牌类型。
        对于无需认证的端点，可省略该项。

        为支持多种令牌类型，MinIO 会按 *原样* 使用该值创建请求认证头。
        根据端点不同，你可能需要包含额外信息。

        例如，对于 Bearer token，请在前面加上 ``Bearer``：

        .. code-block:: shell
           :class: copyable

            mc admin config set ALIAS/ audit_webhook     \
               endpoint="https://webhook-1.example.net"  \
               auth_token="Bearer 1a2b3c4f5e"

        请根据端点要求调整该值。
        自定义认证格式可能类似如下：

        .. code-block:: shell
           :class: copyable

           mc admin config set ALIAS/ audit_webhook     \
              endpoint="https://webhook-1.example.net"  \
              auth_token="ServiceXYZ 1a2b3c4f5e"

        详情请参阅目标服务的文档。

      - 将 ``cert.pem`` 和 ``cert.key`` 替换为要向 HTTP Webhook server 提交的
        x.509 TLS 证书公钥与私钥。
        对于不要求客户端出示 TLS 证书的端点，可省略该项。

审计日志结构
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

MinIO 审计日志类似于以下 JSON 文档：

- ``api.timeToFirstByte`` 和 ``api.timeToResponse`` 字段以纳秒表示。

- 对于 :ref:`纠删码部署 <minio-erasure-coding>`，
  ``tags.objectErasureMap`` 提供与对象相关的以下详细信息：

  - 执行该对象操作所在的 :ref:`服务器池 <minio-intro-server-pool>`。

  - 执行该对象操作所在的 :ref:`纠删码集合 <minio-ec-erasure-set>`。

  - 参与该对象操作的纠删码集合驱动器列表。

.. code-block:: json

   {
      "version": "1",
      "deploymentid": "8ca2b7ad-20cf-4d07-9efb-28b2f519f4a5",
      "time": "2024-02-29T19:39:25.744431903Z",
      "event": "",
      "trigger": "incoming",
      "api": {
         "name": "CompleteMultipartUpload",
         "bucket": "data",
         "object": "test-data.csv",
         "status": "OK",
         "statusCode": 200,
         "rx": 267,
         "tx": 358,
         "txHeaders": 387,
         "timeToFirstByte": "2096989ns",
         "timeToFirstByteInNS": "2096989",
         "timeToResponse": "2111986ns",
         "timeToResponseInNS": "2111986"
      },
      "remotehost": "127.0.0.1",
      "requestID": "17B86CB0ED88EBE9",
      "userAgent": "MinIO (linux; amd64) minio-go/v7.0.67 mc/RELEASE.2024-02-24T01-33-20Z",
      "requestPath": "/data/test-data.csv",
      "requestHost": "minio.example.net:9000",
      "requestQuery": {
         "uploadId": "OGNhMmI3YWQtMjBjZi00ZDA3LTllZmItMjhiMmY1MTlmNGE1LmU3MjNlNWI4LTNiYWYtNDYyNy1hNzI3LWMyNDE3NTVjMmMzNw"
      },
      "requestHeader": {
         "Accept-Encoding": "zstd,gzip",
         "Authorization": "AWS4-HMAC-SHA256 Credential=minioadmin/20240229/us-east-1/s3/aws4_request, SignedHeaders=content-type;host;x-amz-content-sha256;x-amz-date, Signature=ccb3acdc1763509a88a7e4a3d7fe431ef0ee5ca3f66ccb430d5a09326e87e893",
         "Content-Length": "267",
         "Content-Type": "application/octet-stream",
         "User-Agent": "MinIO (linux; amd64) minio-go/v7.0.67 mc/RELEASE.2024-02-24T01-33-20Z",
         "X-Amz-Content-Sha256": "d61969719ee94f43c4e87044229b7a13b54cab320131e9a77259ad0c9344f6d3",
         "X-Amz-Date": "20240229T193925Z"
      },
      "responseHeader": {
         "Accept-Ranges": "bytes",
         "Content-Length": "358",
         "Content-Type": "application/xml",
         "ETag": "1d9fdc88af5e74f5eac0a3dd750ce58e-2",
         "Server": "MinIO",
         "Strict-Transport-Security": "max-age=31536000; includeSubDomains",
         "Vary": "Origin,Accept-Encoding",
         "X-Amz-Id-2": "dd9025bab4ad464b049177c95eb6ebf374d3b3fd1af9251148b658df7ac2e3e8",
         "X-Amz-Request-Id": "17B86CB0ED88EBE9",
         "X-Content-Type-Options": "nosniff",
         "X-Xss-Protection": "1; mode=block"
      },
      "tags": {
         "objectLocation": {
               "name": "Mousepad Template-v03final.jpg",
               "poolId": 1,
               "setId": 1,
               "disks": [
                  "/mnt/drive-1",
                  "/mnt/drive-2",
                  "/mnt/drive-3",
                  "/mnt/drive-4"
               ]
         }
      },
      "accessKey": "minioadmin"
   }
