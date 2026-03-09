1) 下载 MinIO Server
--------------------

以下选项卡给出了在 64 位 Linux 操作系统上通过 RPM、DEB 或二进制方式安装 MinIO 的示例。
RPM 和 DEB 软件包会自动将 MinIO 安装到所需系统路径，并为 ``systemctl`` 创建 ``minio`` 服务。
MinIO 强烈建议优先使用 RPM 或 DEB 安装方式。
如需更新由 ``systemctl`` 管理的部署，请参阅 :ref:`minio-upgrade-systemctl`。

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

      使用以下命令下载最新稳定版 MinIO 二进制文件，并将其安装到系统 ``$PATH`` 中：

      .. code-block:: shell
         :class: copyable

         wget https://dl.min.io/server/minio/release/linux-amd64/minio
         chmod +x minio
         sudo mv minio /usr/local/bin/

2) 运行 MinIO Server
--------------------

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
