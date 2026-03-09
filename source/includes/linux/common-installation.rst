.. start-install-minio-binary-desc

以下选项卡给出了在 64 位 Linux 操作系统上通过 RPM、DEB 或二进制方式安装 MinIO 的示例。
RPM 和 DEB 软件包会自动将 MinIO 安装到所需系统路径，
并为 ``systemctl`` 创建 ``minio`` 服务。
MinIO 强烈建议优先使用 RPM 或 DEB 安装方式。
如需更新由 ``systemctl`` 管理的部署，请参阅 :ref:`minio-upgrade-systemctl`。

.. dropdown:: amd64 (Intel 或 AMD 64 位处理器)
   :open:

   对于运行 Linux 且使用 Intel 或 AMD 64 位处理器的机器，
   请使用以下任一方式下载 MinIO server 安装文件。

   .. tab-set::
   
      .. tab-item:: RPM (RHEL)
         :sync: rpm
   
         使用以下命令下载最新稳定版 MinIO RPM 并完成安装。
   
         .. code-block:: shell
            :class: copyable
            :substitutions:
   
            wget |minio-rpm| -O minio.rpm
            sudo dnf install minio.rpm
   
      .. tab-item:: DEB (Debian/Ubuntu)
         :sync: deb
   
         使用以下命令下载最新稳定版 MinIO DEB 并完成安装：
   
         .. code-block:: shell
            :class: copyable
            :substitutions:
   
            wget |minio-deb| -O minio.deb
            sudo dpkg -i minio.deb
   
      .. tab-item:: Binary
         :sync: binary
   
         使用以下命令下载最新稳定版 MinIO 二进制文件，
         并将其安装到系统 ``$PATH`` 中：

         .. code-block:: shell
            :class: copyable
   
            wget https://dl.min.io/server/minio/release/linux-amd64/minio
            chmod +x minio
            sudo mv minio /usr/local/bin/

.. dropdown:: arm64 (ARM 64 位处理器)
   
   对于运行 Linux 且使用 ARM 64 位处理器的机器，
   请使用以下任一方式下载 MinIO server 安装文件。

   .. tab-set::
   
      .. tab-item:: RPM (RHEL)
         :sync: rpm
   
         使用以下命令下载最新稳定版 MinIO RPM 并完成安装。
   
         .. code-block:: shell
            :class: copyable
            :substitutions:
   
            wget |minio-rpmarm64| -O minio.rpm
            sudo dnf install minio.rpm
   
      .. tab-item:: DEB (Debian/Ubuntu)
         :sync: deb
   
         使用以下命令下载最新稳定版 MinIO DEB 并完成安装：
   
         .. code-block:: shell
            :class: copyable
            :substitutions:
   
            wget |minio-debarm64| -O minio.deb
            sudo dpkg -i minio.deb
   
      .. tab-item:: Binary
         :sync: binary
   
         使用以下命令下载最新稳定版 MinIO 二进制文件，
         并将其安装到系统 ``$PATH`` 中：
   
         .. code-block:: shell
            :class: copyable
   
            wget https://dl.min.io/server/minio/release/linux-arm64/minio
            chmod +x minio
            MINIO_ROOT_USER=admin MINIO_ROOT_PASSWORD=password ./minio server /mnt/data --console-address ":9001"

.. dropdown:: 其他架构

   MinIO 还支持以下架构：

   - ppc64le
   - s390x

   如需获取这些架构对应的二进制、RPM 或 DEB 文件下载说明，
   请参阅 `MinIO download page <https://min.io/download#/linux?ref=docs-install>`__。

.. end-install-minio-binary-desc

.. start-run-minio-binary-desc

运行 :mc-cmd:`minio server` 命令以启动 MinIO server。
指定用作存储目录的卷或文件夹路径。
:mc-cmd:`minio` 进程必须对该路径及其所有子文件夹拥有完整访问权限（``rwx``）：

以下示例使用 ``~/minio-data`` 文件夹：

.. code-block:: shell
   :class: copyable

   mkdir ~/minio-data
   minio server ~/minio-data --console-address ":9001"

:mc:`minio server` 进程会将输出打印到系统控制台，
其内容类似如下：

.. code-block:: shell

   API: http://192.0.2.10:9000  http://127.0.0.1:9000
   RootUser: minioadmin 
   RootPass: minioadmin 

   Console: http://192.0.2.10:9001 http://127.0.0.1:9001     
   RootUser: minioadmin 
   RootPass: minioadmin 

   Command-line: https://minio.pigsty.io/reference/minio-mc.html
      $ mc alias set myminio http://192.0.2.10:9000 minioadmin minioadmin

   Documentation: https://minio.pigsty.io/index.html

   WARNING: Detected default credentials 'minioadmin:minioadmin', we recommend that you change these values with 'MINIO_ROOT_USER' and 'MINIO_ROOT_PASSWORD' environment variables

在浏览器中打开列出的任意 :guilabel:`Console` 地址，
即可进入 :ref:`MinIO Console <minio-console>`，
并使用 :guilabel:`RootUser` 和 :guilabel:`RootPass` 登录。

对于应用程序，请使用 :guilabel:`API` 地址访问 MinIO server 并执行 S3 操作。

以下步骤属于可选项，但建议执行以进一步增强 MinIO 部署的安全性。

.. end-run-minio-binary-desc

.. start-upgrade-minio-binary-desc

以下选项卡给出了在 64 位 Linux 操作系统上通过 RPM、DEB 或二进制方式更新 MinIO 的示例：

.. tab-set::

   .. tab-item:: RPM (RHEL)
      :sync: rpm

      使用以下命令下载最新稳定版 MinIO RPM 并更新现有安装。

      .. code-block:: shell
         :class: copyable
         :substitutions:

         wget |minio-rpm| -O minio.rpm
         sudo dnf update minio.rpm

   .. tab-item:: DEB (Debian/Ubuntu)
      :sync: deb

      使用以下命令下载最新稳定版 MinIO DEB 并升级现有安装：

      .. code-block:: shell
         :class: copyable
         :substitutions:

         wget |minio-deb| -O minio.deb
         sudo dpkg -i minio.deb

   .. tab-item:: Binary
      :sync: binary

      使用以下命令下载最新稳定版 MinIO 二进制文件，
      并覆盖现有二进制文件：

      .. code-block:: shell
         :class: copyable

         wget https://dl.min.io/server/minio/release/linux-amd64/minio
         chmod +x minio
         sudo mv -f ./minio /usr/local/bin/minio

      将 ``/usr/local/bin`` 替换为现有 MinIO 二进制文件所在位置。
      如果尚不确定该路径，请运行 ``which minio`` 进行确认。

.. end-upgrade-minio-binary-desc

.. start-install-minio-systemd-desc

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
   Documentation=https://minio.pigsty.io/index.html
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

The ``minio.service`` file runs as the ``minio-user`` User and Group by default.
You can create the user and group using the ``groupadd`` and ``useradd``
commands. The following example creates the user, group, and sets permissions
to access the folder paths intended for use by MinIO. These commands typically
require root (``sudo``) permissions.

.. code-block:: shell
   :class: copyable

   groupadd -r minio-user
   useradd -M -r -g minio-user minio-user
   chown minio-user:minio-user /mnt/disk1 /mnt/disk2 /mnt/disk3 /mnt/disk4

The specified drive paths are provided as an example. Change them to match
the path to those drives intended for use by MinIO.

Alternatively, change the ``User`` and ``Group`` values to another user and
group on the system host with the necessary access and permissions.

MinIO publishes additional startup script examples on 
:minio-git:`github.com/minio/minio-service <minio-service>`.

To update deployments managed using ``systemctl``, see :ref:`minio-upgrade-systemctl`.

.. end-install-minio-systemd-desc

.. start-install-minio-start-service-desc

.. code-block:: shell
   :class: copyable

   sudo systemctl start minio.service

Use the following commands to confirm the service is online and functional:

.. code-block:: shell
   :class: copyable

   sudo systemctl status minio.service
   journalctl -f -u minio.service

MinIO may log an increased number of non-critical warnings while the 
server processes connect and synchronize. These warnings are typically 
transient and should resolve as the deployment comes online.

.. versionchanged:: RELEASE.2023-02-09T05-16-53Z

   MinIO starts if it detects enough drives to meet the :ref:`write quorum <minio-ec-parity>` for the deployment.
      
   If any drives remain offline after starting MinIO, check and cure any issues blocking their functionality before starting production workloads.

The MinIO service does not automatically start on host reboot.
You must use ``systemctl enable minio.service`` to start the process as part of the host boot.

.. code-block:: shell
   :class: copyable

   sudo systemctl enable minio.service

.. end-install-minio-start-service-desc

.. start-install-minio-restart-service-desc

.. code-block:: shell
   :class: copyable

   sudo systemctl restart minio.service

Use the following commands to confirm the service is online and functional:

.. code-block:: shell
   :class: copyable

   sudo systemctl status minio.service
   journalctl -f -u minio.service

MinIO may log an increased number of non-critical warnings while the 
server processes connect and synchronize. These warnings are typically 
transient and should resolve as the deployment comes online.

.. end-install-minio-restart-service-desc
