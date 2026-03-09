.. start-common-deploy-create-pod-and-containers

本节中的命令会创建以下资源：

- 一个 Podman :podman-docs:`Pod <markdown/podman-pod.1.html>`，用于简化容器间通信。
- 一个配置为使用所选受支持 |KMS| 方案的 KES Server 容器。
- 一个以 :ref:`Single-Node Single-Drive Mode <minio-snsd>` 运行的 MinIO Server 容器。

.. code-block:: shell
   :class: copyable
   :substitutions:

   sudo podman pod create  \
     -p 9000:9000 -p 9001:9001 -p 7373:7373  \
     -v |kescertpath|:/certs  \
     -v |miniodatapath|:/mnt/minio  \
     -v |kesconfigpath|:/etc/default/  \
     -n |namespace|

   sudo podman run -dt  \
     --cap-add IPC_LOCK  \
     --name kes-server  \
     --pod "|namespace|"  \
     -e KES_SERVER=https://127.0.0.1:7373  \
     -e KES_CLIENT_KEY=/certs/kes-server.key  \
     -e KES_CLIENT_CERT=/certs/kes-server.cert  \
     quay.io/minio/kes:|kes-stable| server  \
       --auth  \
       --config=/etc/default/kes-config.yaml  \

   sudo podman run -dt  \
     --name minio-server  \
     --pod "|namespace|"  \
     -e "MINIO_CONFIG_ENV_FILE=/etc/default/minio"  \
     quay.io/minio/minio:|minio-latest| server  \
       --console-address ":9001"

你可以使用以下命令检查容器状态：

.. code-block:: shell
   :class: copyable

   # Should show three pods - one for the Pod, one for KES, and one for MinIO
   sudo podman container ls

如果所有 pod 都处于运行状态，
你就可以在浏览器中访问 http://127.0.0.1:9000 连接到 MinIO 部署，
并使用 MinIO 环境文件中指定的 root 凭证登录。

.. end-common-deploy-create-pod-and-containers

.. start-kes-generate-kes-certs-desc

以下命令会创建两个在创建后 30 天内过期的 TLS 证书：

- 用于保护 KES 与 |KMS| 服务之间通信的 TLS 证书。
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

    # These commands output keys to |kescertpath| and |miniocertpath| on the host operating system

    podman run --rm  \
      -v |kescertpath|:/certs  \
      quay.io/minio/kes:|kes-stable| identity new  kes_server \
        --key  /certs/kes-server.key  \
        --cert /certs/kes-server.cert  \
        kes-server

    podman run --rm  \
      -v |miniocertpath|:/certs  \
      quay.io/minio/kes:|kes-stable| identity new minio_server \
        --key  /certs/minio-kes.key  \
        --cert /certs/minio-kes.cert  \
        minio-server

.. end-kes-generate-kes-certs-desc


.. start-kes-configuration-minio-desc

以下命令假定 ``minio-kes.cert``、``minio-kes.key`` 和 ``kes-server.cert``
证书在指定位置可访问：

.. code-block:: shell
   :class: copyable

   MINIO_ROOT_USER=myminioadmin
   MINIO_ROOT_PASSWORD=minio-secret-key-change-me
   MINIO_VOLUMES="/mnt/data"

   # KES Configurations

   MINIO_KMS_KES_ENDPOINT=https://127.0.0.1:7373
   MINIO_KMS_KES_API_KEY=<API-key-identity-string-from-KES> # Replace with the key string for your credentials
   MINIO_KMS_KES_CAPATH=/certs/server.cert
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

MinIO 会将 :envvar:`MINIO_KMS_KES_KEY_NAME` 这个密钥
用于以下加密操作：

- 加密 MinIO 后端（IAM、配置等）。
- 在请求未包含特定 |EK| 时，
  使用 :ref:`SSE-KMS <minio-encryption-sse-kms>` 加密对象。
- 使用 :ref:`SSE-S3 <minio-encryption-sse-s3>` 加密对象。

``minio-kes`` 证书仅用于 MinIO 部署与 KES Server 之间的 mTLS。
它们不会为其他连接到 MinIO 的客户端启用 TLS。

如果该密钥在根 KMS 上尚不存在，
KES 会自动创建它。

.. end-kes-configuration-minio-desc

.. start-kes-generate-key-desc

.. admonition:: 创建密钥前先解封 Vault
   :class: important

   如果你所选的提供方有此要求，
   则必须先解封底层 |KMS| 实例，
   然后才能创建新的加密密钥。
   更多信息请参考所选 KMS 方案的文档。

MinIO 要求在使用某个 |EK| 执行 |SSE| 操作之前，
该 |EK| 必须已存在于根 KMS 中。
使用 :kes-docs:`kes key create <cli/kes-key/create/>` *或*
:mc-cmd:`mc admin kms key create`
为 |SSE| 创建新的 |EK|。

以下命令使用 :kes-docs:`kes key create <cli/kes-key/create/>`
在根 KMS Server 上添加一个新的 External Key（EK），
供加密 MinIO 后端时使用。

.. code-block:: shell
   :class: copyable
   :substitutions:

   sudo podman run --rm  \
     -v |kescertpath|:/certs  \
     -e KES_SERVER=https://127.0.0.1:7373  \
     -e KES_CLIENT_KEY=/certs/minio-kes.key  \
     -e KES_CLIENT_CERT=/certs/minio-kes.cert  \
     kes:|kes-stable| key create -k my-new-encryption-key

你可以根据自己的使用场景指定任意密钥名称，
例如某个存储桶专用密钥 ``minio-mydata-key``。

.. end-kes-generate-key-desc
