#. 生成供 MinIO 使用的 KES API Key

   使用 :kes-docs:`kes identity new <cli/kes-identity/new>` 命令，
   为 MinIO Server 生成新的 API key：

   .. code-block:: shell
      :class: copyable

      kes identity new

   输出同时包含供 MinIO 使用的 API Key，
   以及供 :kes-docs:`KES Policy 配置 <tutorials/configuration/#policy-configuration>` 使用的 Identity hash。

#. 配置 MinIO 环境文件

   为目标部署中的所有主机创建或修改 MinIO Server 环境文件，
   使其包含以下环境变量：

   .. include:: /includes/common/common-minio-kes.rst
      :start-after: start-kes-configuration-minio-desc
      :end-before: end-kes-configuration-minio-desc

   MinIO 默认在 ``/etc/default/minio`` 查找此文件。
   如果你的部署将环境文件放在其他位置，请修改对应位置的文件。

#. 启动 MinIO

   .. admonition:: KES 操作要求 Vault 已解封
      :class: important

      根据你选择的 KMS 方案，
      你可能需要先将密钥实例解封，才能执行正常的加密操作，包括密钥创建或读取。
      KES 需要已解封的密钥目标才能执行这些操作。

      关于该实例在运行时是否需要 sealed/unsealed，
      请参阅你所选 :kes-docs:`KMS 方案文档 <#supported-kms-targets>`。

      你必须先启动 KES，再启动 MinIO。
      MinIO 部署在启动过程中需要访问 KES。

   你可以使用 :mc:`mc admin service restart` 命令重启 MinIO：

   .. code-block:: shell
      :class: copyable

      mc admin service restart ALIAS

#. 生成新的加密密钥

   .. include:: /includes/common/common-minio-kes.rst
      :start-after: start-kes-generate-key-desc
      :end-before: end-kes-generate-key-desc

#. 为存储桶启用 SSE-KMS

   .. include:: /includes/common/common-minio-kes.rst
      :start-after: start-kes-enable-sse-kms-desc
      :end-before: end-kes-enable-sse-kms-desc
