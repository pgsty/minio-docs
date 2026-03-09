.. start-kes-configuration-hashicorp-vault-desc

|KES| 使用 YAML 格式的配置文件。
以下 YAML 给出了使用 HashiCorp Vault 作为根 |KMS| 所需的最小字段集。
你必须根据自己的部署环境修改此 YAML。

.. code-block:: shell
   :class: copyable
   :substitutions:

   address: 0.0.0.0:7373

   # Disable the root administrator identity, as we do not need that level of access for
   # supporting SSE operations.
   admin:
     identity: disabled

   # Specify the TLS keys generated in the previous step here
   # For production environments, use keys signed by a known and trusted Certificate Authority (CA).
   tls:
     key:  |kesconfigcertpath|kes-server.key
     cert: |kesconfigcertpath|kes-server.cert

     # Specify the path to CAs used by KES for validating client certificates
     # This can alternatively be a single CA
     # KES uses these CAs in addition to the system trust store for validating client certificates.

     ca: |kesconfigcertpath|CAs/

   # Sets access policies for KES
   # The `minio` policy grants access to the listed APIs.
   policy:
     minio:
       allow:
       - /v1/key/create/*   # You can replace these wildcard '*' with a string prefix to restrict key names
       - /v1/key/generate/* # e.g. '/minio-'
       - /v1/key/decrypt/*
       - /v1/key/bulk/decrypt
       - /v1/key/list/*
       - /v1/status
       - /v1/metrics
       - /v1/log/audit
       - /v1/log/error
       identities:
       - MINIO_API_KEY_HASH # Replace with the hash output returned from kes identity new

   # Specify the connection information for the Vault server.
   # The endpoint should be resolvable from the host.
   # This example assumes that Vault is configured with an AppRole ID and
   # Secret for use with KES.
   keystore:
     vault:
       endpoint: https://HOSTNAME:8200
       engine: "/path/to/engine" # Replace with the path to the K/V Engine
       version: "v1|v2" # Specify v1 or v2 depending on the version of the K/V Engine
       approle:
         id: "VAULTAPPID"     # HashiCorp Vault AppRole ID
         secret: "VAULTAPPSECRET" # HashiCorp Vault AppRole Secret ID
         retry: 15s
       status:
         ping: 10s
       # Required if Vault uses certificates signed by an unknown CA,
       # e.g. self-signed or internal (non-globally trusted).
       # Replace this value with the full path to the Vault CA certificate.
       tls:
         ca: vault-tls-CA.cert

.. end-kes-configuration-hashicorp-vault-desc


.. start-kes-prereq-hashicorp-vault-desc

本流程假定本地主机可访问一个现有的 `HashiCorp Vault <https://www.vaultproject.io/>`__ 安装。
出于本流程目的，Vault 的
`Quick Start <https://learn.hashicorp.com/tutorials/vault/getting-started-install>`__
已足够提供所需基础。
关于部署和配置的具体指导，请参考
`Vault Documentation <https://learn.hashicorp.com/vault>`__。

.. admonition:: KES 操作要求 Vault 已解封
   :class: important

   你必须解封 Vault 实例，才能执行包括密钥创建和检索在内的任何加密操作。
   如果配置的 Vault 服务处于 sealed 状态，KES 会返回错误。

   如果你重启或以其他方式重新封存 Vault 实例，
   KES 将无法对 Vault 执行任何加密操作。
   你必须解封 Vault 才能保证其正常运行。

   更多信息请参见 Vault 关于
   `Seal/Unseal <https://www.vaultproject.io/docs/concepts/seal>`__
   的文档。

.. end-kes-prereq-hashicorp-vault-desc

.. start-kes-vault-seal-unseal-desc

.. admonition:: KES 操作要求 Vault 已解封
   :class: important

   你必须解封 Vault 实例，才能执行包括密钥创建或检索在内的正常加密操作。
   更多信息请参见 Vault 关于
   `Seal/Unseal <https://www.vaultproject.io/docs/concepts/seal>`__
   的文档。

.. end-kes-vault-seal-unseal-desc
