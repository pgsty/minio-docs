.. The following sections are common among all KES-related tutorials
.. Use the /includes/<platform>/common-minio-kes.rst file for platform-specific overrides.

.. start-kes-encrypted-backend-desc

在 MinIO 部署上启用 |SSE| 后，
会自动使用默认加密密钥对该部署的后端数据进行加密。

MinIO 必须能够访问 KES 和外部 KMS，
才能解密后端并正常启动。
KMS 必须维护并提供对 :envvar:`MINIO_KMS_KES_KEY_NAME` 的访问。
之后你不能再禁用 KES，
也不能在后续“撤销”该 |SSE| 配置。

.. end-kes-encrypted-backend-desc

.. start-kes-new-existing-minio-deployment-desc

本流程说明如何修改 MinIO 部署的启动环境变量，
以便通过 KES 和指定的 KMS 目标启用 |SSE|。

关于新的生产部署，请参见
:ref:`多机多盘 (Distributed) <minio-mnmd>` 教程。
关于新的本地或评估部署，请参见
:ref:`单机单盘 <minio-snsd>` 教程。

在为部署创建环境文件时，请暂停并返回本教程，
加入支持 |SSE| 所需的环境变量。

对于现有 MinIO 部署，
你可以修改现有环境文件，并按本流程说明重启部署。

.. end-kes-new-existing-minio-deployment-desc

.. start-kes-generate-kes-certs-desc

以下命令会创建两个在创建后 30 天内过期的 TLS 证书：

- 用于保护 KES 与 KMS 之间通信的 TLS 证书。
- 用于 MinIO 向 KES 执行 mTLS 认证的 TLS 证书。

.. admonition:: 生产环境中请谨慎使用
   :class: important

   **不要** 将本流程生成的 TLS 证书
   用于任何长期开发环境或生产环境。

   请遵循所在组织或行业关于 TLS 证书生成和管理的最佳实践。
   关于如何创建有效证书
   （例如格式正确、未过期且受信任）的完整指南
   不在本流程范围之内。

.. code-block:: shell
   :class: copyable
   :substitutions:

   # These commands output keys to |kescertpath|
   # and |miniocertpath| respectively

   kes identity new kes_server \
     --key  |kescertpath|/kes-server.key  \
     --cert |kescertpath|/kes-server.cert  \
     --ip   "127.0.0.1"  \
     --dns  localhost

   kes identity new minio_server \
     --key  |miniocertpath|/minio-kes.key  \
     --cert |miniocertpath|/minio-kes.cert  \
     --ip   "127.0.0.1"  \
     --dns  localhost

``--ip`` 和 ``--dns`` 参数用于设置证书的 IP 和 DNS
``SubjectAlternativeName``。
上述示例假定所有组件（KMS、MinIO 和 KES）
都部署在同一台本地主机上，
且可通过 ``localhost`` 或 ``127.0.0.1`` 访问。
你可以根据本地主机的网络配置指定额外的 IP 或主机名。

.. end-kes-generate-kes-certs-desc

.. start-kes-minio-start-server-desc

在终端或 shell 中运行以下命令，
以前台进程方式启动 MinIO server。

.. code-block:: shell
   :class: copyable
   :substitutions:

   export MINIO_CONFIG_ENV_FILE=|minioconfigpath|/minio
   minio server --console-address :9001

.. end-kes-minio-start-server-desc

.. start-kes-start-server-desc

在终端或 shell 中运行以下命令，
以前台进程方式启动 KES server：

.. code-block:: shell
   :class: copyable
   :substitutions:

   sudo setcap cap_ipc_lock=+ep $(readlink -f $(which kes))

   kes server --auth=off --config=|kesconfigpath|/kes-config.yaml

第一条命令允许 |KES| 在不以 root 身份运行的情况下，
使用 `mlock <http://man7.org/linux/man-pages/man2/mlock.2.html>`__ 系统调用。
``mlock`` 可确保操作系统不会将内存中的数据写入磁盘
（swap memory），
从而降低加密操作数据在任意时刻被写入不安全磁盘的风险。
KES 0.21.0 及更高版本会在宿主机操作系统支持时自动检测并启用 ``mlock``。
0.20.0 及更早版本则需要显式为 KES 指定 ``--mlock`` 参数。

第二条命令使用上一步创建的配置文件，
以前台方式启动 KES server。
``--auth=off`` 会禁用对客户端 TLS 证书的严格校验。
如果 MinIO 客户端或根 KMS server 使用自签名证书，
则必须指定此选项。

|KES| 默认监听 ``7373`` 端口。
你可以在当前终端会话中监控 server 日志。
如果你运行 |KES| 时未将其绑定到当前 shell 会话
（例如使用 ``nohup``），
则应使用该方式对应的日志系统
（例如 ``nohup.txt``）。

.. end-kes-start-server-desc

.. start-kes-generate-key-desc

MinIO 要求在使用某个 |EK| 执行 |SSE| 操作之前，
该 |EK| 必须已存在于 KMS 中。
使用 ``kes key create`` *或*
:mc-cmd:`mc admin kms key create`
为 |SSE| 添加新的 |EK|。

以下命令使用 :mc-cmd:`mc admin kms key create`
在 KMS server 上添加一个新的 External Key（EK），
供加密 MinIO 后端时使用。

.. code-block:: shell
   :class: copyable

   mc admin kms key create ALIAS KEYNAME

.. end-kes-generate-key-desc

.. start-kes-configuration-minio-desc

将以下内容添加到每台 MinIO 主机上的 MinIO 环境文件中。
关于基础 MinIO 环境文件的更详细说明，
请参见 :ref:`minio-snsd`、:ref:`minio-snmd`
或 :ref:`minio-mnmd` 教程。

.. code-block:: shell
   :class: copyable
   :substitutions:

   # Add these environment variables to the existing environment file

   MINIO_KMS_KES_ENDPOINT=https://HOSTNAME:7373
   MINIO_KMS_KES_API_KEY="kes:v1:ACTpAsNoaGf2Ow9o5gU8OmcaG6Af/VcZ1Mt7ysuKoBjv"

   # Allows validation of the KES Server Certificate (Self-Signed or Third-Party CA)
   # Change this path to the location of the KES CA Path
   MINIO_KMS_KES_CAPATH=|kescertpath|/kes-server.cert

   # Sets the default KMS key for the backend and SSE-KMS/SSE-S3 Operations)
   MINIO_KMS_KES_KEY_NAME=minio-backend-default-key

将 ``HOSTNAME`` 替换为 KES server 的 IP 地址或主机名。
如果 MinIO server 所在主机无法解析或访问指定的 ``HOSTNAME``，
该部署可能会返回错误，或启动失败。

- 如果只使用一台 KES server 主机，请指定该主机的 IP 或主机名。
- 如果使用多台 KES server 主机，请指定各主机 IP 或主机名的逗号分隔列表。

MinIO 会将 :envvar:`MINIO_KMS_KES_KEY_NAME` 这个密钥
用于以下加密操作：

- 加密 MinIO 后端（IAM、配置等）。
- 在请求未包含特定 |EK| 时，
  使用 :ref:`SSE-KMS <minio-encryption-sse-kms>` 加密对象。
- 使用 :ref:`SSE-S3 <minio-encryption-sse-s3>` 加密对象。

.. end-kes-configuration-minio-desc

.. start-kes-enable-sse-kms-desc

使用 MinIO :mc:`mc` CLI，
通过生成的密钥启用存储桶默认 SSE-KMS：

以下命令会：

- 为 MinIO 部署创建一个新的 :ref:`alias <alias>`。
- 创建一个用于存储加密数据的新存储桶。
- 在该存储桶上启用 SSE-KMS 加密。

.. code-block:: shell
   :class: copyable

   mc alias set local http://127.0.0.1:9000 ROOTUSER ROOTPASSWORD

   mc mb local/encryptedbucket
   mc encrypt set SSE-KMS encrypted-bucket-key ALIAS/encryptedbucket

使用 :mc:`mc cp` 或任何带有 ``PutObject`` 函数的 S3 兼容 SDK
将文件写入该存储桶。
然后你可以对该文件执行 :mc:`mc stat`，
以确认其关联的加密元数据。

.. end-kes-enable-sse-kms-desc

.. -----------------------------------------------------------------------------

.. The following sections are common descriptors associated to the KES
   configuration.

.. start-kes-conf-address-desc

KES server 启动时监听的网络地址和端口。
默认在所有主机网络接口上的 ``7373`` 端口监听。

.. end-kes-conf-address-desc


.. start-kes-conf-root-desc

KES 超级用户（``root``）身份所对应的 identity。
如果客户端使用的 TLS 证书哈希
（``kes identity of client.cert``）
与该值匹配，
则该客户端可以访问全部 KES API 操作。

指定 ``disabled`` 可移除 root identity，
并仅依赖 ``policy`` 配置来控制 KES 的身份与访问管理。

.. end-kes-conf-root-desc


.. start-kes-conf-tls-desc

KES 用于建立 TLS 安全通信的 TLS 私钥和证书。
分别在 ``key`` 和 ``cert`` 字段中，
指定私有 ``.key`` 和公开 ``.cert`` 的完整路径。

.. end-kes-conf-tls-desc

.. start-kes-conf-policy-desc

指定一个或多个
:minio-git:`policies <kes/wiki/Configuration#policy-configuration>`
来控制对 KES server 的访问。

MinIO |SSE| 需要访问以下 KES 加密 API：

- ``/v1/key/create/*``
- ``/v1/key/generate/*``
- ``/v1/key/decrypt/*``

指定额外的密钥不会扩展 MinIO |SSE| 功能，
反而可能违背不向客户端提供不必要加密密钥操作访问权限的安全最佳实践。

你可以通过在 ``*`` 前指定前缀，
限制 MinIO 在执行 |SSE| 时可创建的密钥名称范围。
例如，``minio-sse-*`` 只允许创建、生成或解密带有
``minio-sse-`` 前缀的密钥。

|KES| 使用 mTLS 为连接的客户端授权，
方式是将 TLS 证书的哈希与各已配置 policy 中的 ``identities`` 进行比较。
使用 ``kes identity of`` 命令计算 MinIO mTLS 证书的 identity，
并将其加入 ``policy.<NAME>.identities`` 数组，
即可将 MinIO 关联到 ``<NAME>`` policy。

.. end-kes-conf-policy-desc

.. start-kes-conf-keys-desc

指定一个密钥数组，
其中的密钥都必须已经存在于根 KMS 中，
|KES| 才能成功启动。
如果这些密钥不存在，KES 会尝试创建它们；
若任意密钥创建失败，KES 会以错误退出。
在完成对所有指定密钥的校验之前，
KES 不会接受任何客户端请求。

.. end-kes-conf-keys-desc

.. -----------------------------------------------------------------------------

.. The following sections include common admonitions/notes across all KES
   properties. These are used in the following pages:

   - /source/security/server-side-encryption/server-side-encryption-sse-kms.rst
   - /source/security/server-side-encryption/server-side-encryption-sse-s3.rst
   - /source/security/server-side-encryption/server-side-encryption-sse-c.rst

.. start-kes-play-sandbox-warning

.. important::

   MinIO KES ``Play`` sandbox 是公开环境，
   并会为所有创建的 External Keys（EK）授予 root 级访问权限。
   任何存储在 ``Play`` sandbox 上的 |EK|
   都可能随时被访问或销毁，
   从而使受保护数据暴露风险或永久不可读。

   - **切勿** 使用 ``Play`` sandbox 保护你无法承受丢失或泄露的数据。

   - **切勿** 使用会暴露组织私有、机密或内部命名约定的名称来生成 |EK|。

   - **切勿** 在生产环境中使用 ``Play`` sandbox。

.. end-kes-play-sandbox-warning
