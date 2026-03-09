
.. start-kes-minio-start-service-desc

对于新的 MinIO 部署，
请在每台 MinIO 主机上运行以下命令以启动服务：

.. code-block:: shell
   :class: copyable

   systemctl start minio

对于现有 MinIO 部署，
请在每台 MinIO 主机上运行以下命令以重启服务：

.. code-block:: shell
   :class: copyable

   systemctl reload minio
   systemctl restart minio

.. end-kes-minio-start-service-desc

.. start-kes-generate-kes-certs-prod-desc

KES 要求所有客户端连接都使用 TLS，
包括来自 MinIO 的连接。
有关如何为 MinIO 部署启用 TLS 的更多信息，
请参见 :ref:`minio-tls`。

根据所选 KMS 目标的配置，
你可能还需要为 KES 创建一组专用 TLS 证书，
用于连接到 KMS 并完成认证。

请遵循贵组织关于生成生产就绪 TLS 证书的最佳实践。

将证书及其对应私钥放置在一个目录中，
并确保 KES 服务用户有权限访问这些文件并读取该目录内容。
例如：

.. code-block:: shell
   :substitutions:

   -rw-r--r-- 1 kes:kes |kescertpath|/kes-server.cert
   -rw-r--r-- 1 kes:kes |kescertpath|/kes-server.key

   # If the Vault certs are self-signed or use a non-global CA
   # Include those CA certs as well

   -rw-r--r-- 1 kes:kes |kescertpath|/vault-CA.cert

.. end-kes-generate-kes-certs-prod-desc

.. start-kes-generate-key-desc

MinIO 要求在使用某个 |EK| 执行 |SSE| 操作之前，
该 |EK| 必须已存在于根 KMS 中。
使用 ``kes key create`` *或*
:mc-cmd:`mc admin kms key create`
为 |SSE| 添加新的 |EK|。

以下命令使用 ``kes key create`` 命令，
在根 KMS 服务器上添加一个新的 External Key（EK），
供加密 MinIO 后端时使用。

.. code-block:: shell
   :class: copyable

   mc admin kms key create ALIAS KEYNAME

.. end-kes-generate-key-desc
