.. start-kes-generate-kes-certs-desc

以下命令会创建两个在创建后 30 天内过期的 TLS 证书：

- 用于 KES 保护其与 KMS 部署之间通信的 TLS 证书。
- 用于 MinIO 向 KES 执行 mTLS 认证的 TLS 证书。

.. admonition:: 生产环境中请谨慎使用
   :class: important

   **不要** 将本流程生成的 TLS 证书
   用于任何长期开发环境或生产环境。

   请遵循所在组织或行业关于 TLS 证书生成和管理的最佳实践。
   关于如何创建有效证书
   （例如格式正确、未过期且受信任）的完整指南
   不在本流程范围之内。

.. code-block:: powershell
   :class: copyable
   :substitutions:

   # These commands output the certificates to |kescertpath|

   C:\kes.exe identity new \
     --key  |kescertpath|\kes-server.key \
     --cert |kescertpath|\kes-server.cert \
     --ip   "127.0.0.1" \
     --dns  localhost

   C:\kes.exe identity new \
     --key  |miniocertpath|\minio-kes.key \
     --cert |miniocertpath|\minio-kes.cert \
     --ip   "127.0.0.1" \
     --dns  localhost

``--ip`` 和 ``--dns`` 参数用于设置证书的 IP 和 DNS
``SubjectAlternativeName``。
上述示例假定所有组件（KMS、MinIO 和 KES）
都部署在同一台本地主机上，
且可通过 ``localhost`` 或 ``127.0.0.1`` 访问。
你可以根据本地主机的网络配置指定额外的 IP 或主机名。

根据你的 KMS 配置，
你可能需要将 ``kes-server.cert`` 作为受信任的 Certificate Authority 传递给客户端。
关于如何信任第三方 CA，
请参见你所选
:kes-docs:`受支持 KMS 目标 <#supported-kms-targets>`
的客户端文档。

.. end-kes-generate-kes-certs-desc

.. start-kes-minio-start-server-desc

在终端或 shell 中运行以下命令，
以前台进程方式启动 MinIO server。

.. code-block:: powershell
   :class: copyable
   :substitutions:

   export MINIO_CONFIG_ENV_FILE=|minioconfigpath|\config\minio
   C:\minio.exe server --console-address :9001

.. end-kes-minio-start-server-desc

.. start-kes-generate-key-desc

MinIO 要求在使用某个 |EK| 执行 |SSE| 操作之前，
该 |EK| 必须已存在于根 KMS 中。
使用 ``kes key create`` *或*
:mc-cmd:`mc admin kms key create`
为 |SSE| 创建新的 |EK|。

以下命令使用 ``kes key create`` 命令，
在根 KMS Server 上创建一个新的 External Key（EK），
供加密 MinIO 后端时使用。

.. code-block:: powershell
   :class: copyable
   :substitutions:

   export KES_SERVER=https://127.0.0.1:7373
   export KES_CLIENT_KEY=|miniocertpath|\minio-kes.key
   export KES_CLIENT_CERT=|miniocertpath|\minio-kes.cert

   C:\kes.exe key create -k encrypted-bucket-key

.. end-kes-generate-key-desc

.. start-kes-new-existing-minio-deployment-desc

本流程说明如何修改 MinIO 部署的启动环境变量，
以便通过 KES 和根 KMS 启用 |SSE|。
关于如何创建新部署，
请参考 :ref:`单机单盘 <minio-snsd>` 教程。

在为部署创建环境文件时，请暂停并返回本教程，
加入支持 |SSE| 所需的环境变量。

对于现有 MinIO 部署，
你可以修改现有环境文件，并按本流程说明重启部署。

.. end-kes-new-existing-minio-deployment-desc

.. start-kes-configuration-minio-desc

将以下内容添加到 Windows 主机上的 MinIO 环境文件中。
关于基础 MinIO 环境文件的更详细说明，
请参见 :ref:`minio-snsd` 教程。

以下命令假定 ``minio-kes.cert``、``minio-kes.key`` 和 ``kes-server.cert``
证书在指定位置可访问：

.. code-block:: powershell
   :class: copyable
   :substitutions:

   # Add these environment variables to the existing environment file

   MINIO_KMS_KES_ENDPOINT=https://127.0.0.1:7373
   MINIO_KMS_KES_API_KEY=<API-key-identity-string-from-KES>
   MINIO_KMS_KES_CAPATH=|miniocertpath|\kes-server.cert
   MINIO_KMS_KES_KEY_NAME=minio-backend-default-key


.. note::

   - API key 是与 KES Server 认证的首选方式，
     因为它为 KES Server 提供了更精简且更安全的认证流程。

   - 或者，你也可以指定
     :envvar:`MINIO_KMS_KES_KEY_FILE` 和
     :envvar:`MINIO_KMS_KES_CERT_FILE`，
     而不是 :envvar:`MINIO_KMS_KES_API_KEY`。

     API key 与基于证书的认证互斥。
     二者只能指定其一：要么 API key 变量，
     要么 Key File 和 Cert File 变量。

   - 本站文档统一使用 API key。

   .. code-block:: shell
      :substitutions:

      MINIO_KMS_KES_CERT_FILE=|miniocertpath|\minio-kes.cert
      MINIO_KMS_KES_KEY_FILE=|miniocertpath|\minio-kes.key


MinIO 会将 :envvar:`MINIO_KMS_KES_KEY_NAME` 这个密钥
用于以下加密操作：

- 加密 MinIO 后端（IAM、配置等）。
- 在请求未包含特定 |EK| 时，
  使用 :ref:`SSE-KMS <minio-encryption-sse-kms>` 加密对象。
- 使用 :ref:`SSE-S3 <minio-encryption-sse-s3>` 加密对象。

``minio-kes`` 证书仅用于 MinIO 部署与 KES Server 之间的 mTLS。
它们不会为其他连接到 MinIO 的客户端启用 TLS。

.. end-kes-configuration-minio-desc
