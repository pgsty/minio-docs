1) 下载 MinIO Server
~~~~~~~~~~~~~~~~~~~~

.. include:: /includes/linux/common-installation.rst
   :start-after: start-install-minio-binary-desc
   :end-before: end-install-minio-binary-desc

2) 创建 ``systemd`` 服务文件
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

``.deb`` 或 ``.rpm`` 软件包会将以下
`systemd <https://www.freedesktop.org/wiki/Software/systemd/>`__ 服务文件
安装到 ``/usr/lib/systemd/system/minio.service``。
对于二进制安装方式，请在所有 MinIO 主机上手动创建该文件。

.. note::
   
   ``systemd`` 会先检查 ``/etc/systemd/...`` 路径，再检查 ``/usr/lib/systemd/...`` 路径，
   并使用它找到的第一个文件。
   为避免配置冲突或意外选项，请确认该文件仅存在于
   ``/usr/lib/systemd/system/minio.service`` 路径下。

   关于文件路径搜索顺序的详细信息，请参阅
   `systemd.unit 的 man page <https://www.man7.org/linux/man-pages/man5/systemd.unit.5.html>`__。
    
.. code-block:: shell
   :class: copyable

   [Unit]
   Description=MinIO
   Documentation=https://silo.pigsty.cc/index.html
   Wants=network-online.target
   After=network-online.target
   AssertFileIsExecutable=/usr/local/bin/minio

   [Service]
   WorkingDirectory=/usr/local

   User=minio-user
   Group=minio-user
   ProtectProc=invisible

   EnvironmentFile=-/etc/default/minio
   ExecStartPre=/bin/bash -c "if [ -z \"${MINIO_VOLUMES}\" ]; then echo \"Variable MINIO_VOLUMES not set in /etc/default/minio\"; exit 1; fi"
   ExecStart=/usr/local/bin/minio server $MINIO_OPTS $MINIO_VOLUMES

   # MinIO RELEASE.2023-05-04T21-44-30Z adds support for Type=notify (https://www.freedesktop.org/software/systemd/man/systemd.service.html#Type=)
   # This may improve systemctl setups where other services use `After=minio.server`
   # Uncomment the line to enable the functionality
   # Type=notify

   # Let systemd restart this service always
   Restart=always

   # Specifies the maximum file descriptor number that can be opened by this process
   LimitNOFILE=65536

   # Specifies the maximum number of threads this process can create
   TasksMax=infinity

   # Disable timeout logic and wait until process is stopped
   TimeoutStopSec=infinity
   SendSIGKILL=no

   [Install]
   WantedBy=multi-user.target

   # Built for ${project.name}-${project.version} (${project.name})

``minio.service`` 文件默认使用 ``minio-user`` 作为 User 和 Group 运行。
你可以使用 ``groupadd`` 和 ``useradd`` 命令创建该用户和组。
以下示例会创建用户和组，并设置对 MinIO 目标文件夹路径的访问权限。
这些命令通常需要 root（``sudo``）权限。

.. code-block:: shell
   :class: copyable

   groupadd -r minio-user
   useradd -M -r -g minio-user minio-user
   chown minio-user:minio-user /mnt/data

此示例中的驱动器路径由 ``MINIO_VOLUMES`` 环境变量指定。
请同时修改此处和环境变量文件中的值，使其与 MinIO 实际使用的驱动器路径一致。

或者，也可以将 ``User`` 和 ``Group`` 的值改为系统主机上其他具备所需访问权限的用户和组。

MinIO 在 :minio-git:`github.com/minio/minio-service <minio-service>`
中提供了更多启动脚本示例。

如需更新由 ``systemctl`` 管理的部署，请参阅 :ref:`minio-upgrade-systemctl`。


3) 创建环境变量文件
~~~~~~~~~~~~~~~~~~~

在 ``/etc/default/minio`` 路径下创建环境变量文件。
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

.. versionadded:: Server RELEASE.2024-03-03T17-50-39Z

如果同时满足以下所有条件，MinIO 会自动生成唯一的 root 凭证：

- 正在运行 :kes-docs:`KES <tutorials/getting-started/>` Release 2024-03-01T18-06-46Z 或更高版本
  
- **未** 定义：
  
  - ``MINIO_ROOT_USER`` 变量
  - ``MINIO_ROOT_PASSWORD`` 变量 
  
- **已满足**：
  
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


4) 启动 MinIO 服务
~~~~~~~~~~~~~~~~~~

在本地主机上执行以下命令，以服务方式启动 MinIO |SNSD| 部署：

.. include:: /includes/linux/common-installation.rst
   :start-after: start-install-minio-start-service-desc
   :end-before: end-install-minio-start-service-desc

``journalctl`` 输出应类似如下：

.. code-block:: shell

   Status:         1 Online, 0 Offline. 
   API: http://192.168.2.100:9000  http://127.0.0.1:9000       
   RootUser: myminioadmin 
   RootPass: minio-secret-key-change-me 
   Console: http://192.168.2.100:9001 http://127.0.0.1:9001    
   RootUser: myminioadmin 
   RootPass: minio-secret-key-change-me 

   Command-line: https://silo.pigsty.cc/reference/minio-mc.html
      $ mc alias set myminio http://10.0.2.100:9000 myminioadmin minio-secret-key-change-me

   Documentation: https://silo.pigsty.cc/index.html

``API`` 区块列出了客户端可访问 MinIO S3 API 的网络接口和端口。
``Console`` 区块列出了客户端可访问 MinIO Web Console 的网络接口和端口。

5) 连接到 MinIO 服务
~~~~~~~~~~~~~~~~~~~~

.. include:: /includes/common/common-deploy.rst
   :start-after: start-common-deploy-connect-to-minio-deployment
   :end-before: end-common-deploy-connect-to-minio-deployment
