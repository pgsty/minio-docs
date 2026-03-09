.. start-common-deploy-create-environment-file-single-drive

在 ``/etc/default/minio`` 创建环境变量文件。
对于 Windows 主机，请指定类似 ``C:\minio\config`` 的 Windows 风格路径。
MinIO Server 容器可以将此文件用作所有 :ref:`环境变量 <minio-server-environment-variables>` 的来源。

以下示例给出了可作为起点的环境文件：

.. code-block:: shell
   :class: copyable

   # MINIO_ROOT_USER and MINIO_ROOT_PASSWORD sets the root account for the MinIO server.
   # This user has unrestricted permissions to perform S3 and administrative API operations on any resource in the deployment.
   # Omit to use the default values 'minioadmin:minioadmin'.
   # MinIO recommends setting non-default values as a best practice, regardless of environment

   MINIO_ROOT_USER=myminioadmin
   MINIO_ROOT_PASSWORD=minio-secret-key-change-me

   # MINIO_VOLUMES sets the storage volume or path to use for the MinIO server.

   MINIO_VOLUMES="/mnt/data"

   # MINIO_OPTS sets any additional commandline options to pass to the MinIO server.
   # For example, `--console-address :9001` sets the MinIO Console listen port
   MINIO_OPTS="--console-address :9001"

根据你的部署要求，补充其他所需环境变量。

.. end-common-deploy-create-environment-file-single-drive

.. start-common-deploy-create-unique-root-credentials

.. versionadded:: Server RELEASE.2024-03-03T17-50-39Z

   如果同时满足以下所有条件，MinIO 会自动生成唯一的 root 凭证：

   - :kes-docs:`KES <tutorials/getting-started/>` Release 2024-03-01T18-06-46Z or later running
  
   - **未** 定义：
  
     - ``MINIO_ROOT_USER`` 变量
     - ``MINIO_ROOT_PASSWORD`` 变量
  
   - **已**：
  
     - 使用 :kes-docs:`受支持的 KMS 目标 <#supported-kms-targets>` 完成 KES 配置
     - 通过 :ref:`MinIO 环境变量 <minio-disable-root-access>` 禁用 root 访问

   当这些条件在启动时满足后，
   MinIO 会使用 KMS 和 `hash-based message authentication code (HMAC) <https://en.wikipedia.org/wiki/HMAC>`__
   为该部署生成唯一的 root 凭证。

   如果 MinIO 生成了此类凭证，
   则用于生成这些凭证的密钥 **必须** 保持不变，并且必须持续存在。
   该部署中的所有数据都使用此密钥加密！

   如果要轮换已生成的 root 凭证，
   请先在 KMS 中生成新密钥，然后将 :envvar:`MINIO_KMS_KES_KEY_NAME`
   的值更新为该新密钥。

.. end-common-deploy-create-unique-root-credentials

.. start-common-deploy-create-environment-file-multi-drive

在 ``/etc/default/minio`` 创建环境变量文件。
对于 Windows 主机，请指定类似 ``C:\minio\config`` 的 Windows 风格路径。
MinIO Server 容器可以将此文件用作所有 :ref:`环境变量 <minio-server-environment-variables>` 的来源。

以下示例给出了可作为起点的环境文件：

.. code-block:: shell
   :class: copyable

   # MINIO_ROOT_USER and MINIO_ROOT_PASSWORD sets the root account for the MinIO server.
   # This user has unrestricted permissions to perform S3 and administrative API operations on any resource in the deployment.
   # Omit to use the default values 'minioadmin:minioadmin'.
   # MinIO recommends setting non-default values as a best practice, regardless of environment.

   MINIO_ROOT_USER=myminioadmin
   MINIO_ROOT_PASSWORD=minio-secret-key-change-me

   # MINIO_VOLUMES sets the storage volumes or paths to use for the MinIO server.
   # The specified path uses MinIO expansion notation to denote a sequential series of drives between 1 and 4, inclusive.
   # All drives or paths included in the expanded drive list must exist *and* be empty or freshly formatted for MinIO to start successfully.

   MINIO_VOLUMES="/data-{1...4}"

   # MINIO_OPTS sets any additional commandline options to pass to the MinIO server.
   # For example, `--console-address :9001` sets the MinIO Console listen port
   MINIO_OPTS="--console-address :9001"

根据你的本地部署要求，补充其他所需环境变量。
.. end-common-deploy-create-environment-file-multi-drive

.. start-common-deploy-connect-to-minio-deployment

.. tab-set::

   .. tab-item:: MinIO Console

      你可以在浏览器中输入 MinIO server ``Console`` 区块中的任意主机名或 IP 地址来访问 MinIO Console，
      例如 http://localhost:9001。

      使用容器所指定环境文件中的 :envvar:`MINIO_ROOT_USER`
      和 :envvar:`MINIO_ROOT_PASSWORD` 登录。

      每个 MinIO server 都内置自己的 MinIO Console。

      如果本地主机防火墙允许外部访问 Console 端口，
      同一网络中的其他主机也可以通过本地主机的 IP 或主机名访问 Console。

   .. tab-item:: MinIO CLI (mc)

      你可以在 Terminal 或 Shell 中使用 :ref:`MinIO Client <minio-client>` (:mc:`mc`)
      访问 MinIO 部署。
      关于如何安装 :mc:`mc`，请参阅 :ref:`MinIO Client Installation Quickstart <mc-install>`。

      为该 MinIO 部署创建新的 :mc:`alias <mc alias set>`。
      指定 MinIO Server ``API`` 区块中的任意主机名或 IP 地址，
      例如 http://localhost:9000。

      .. code-block:: shell
         :class: copyable

         mc alias set myminio http://localhost:9000 myminioadmin minio-secret-key-change-me

      - 将 ``myminio`` 替换为你希望使用的别名名称。

      - 将 ``myminioadmin`` 替换为容器所指定环境文件中的 :envvar:`MINIO_ROOT_USER` 值。

      - 将 ``minio-secret-key-change-me`` 替换为容器所指定环境文件中的 :envvar:`MINIO_ROOT_PASSWORD` 值。

      之后，你就可以使用任意 :mc:`mc` 命令与该容器交互。
      如果本地主机防火墙允许外部访问 MinIO S3 API 端口，
      同一网络中的其他主机也可以通过本地主机的 IP 或主机名访问该 MinIO 部署。

.. end-common-deploy-connect-to-minio-deployment
