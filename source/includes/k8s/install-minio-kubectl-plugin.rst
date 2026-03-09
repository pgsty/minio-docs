你可以通过 Kubernetes Krew 插件管理器安装 MinIO 插件，
也可以手动下载插件二进制并安装到本地主机：

.. tab-set::

   .. tab-item:: Krew Plugin Manager

      Krew 是由 `Kubernetes SIG CLI group <https://github.com/kubernetes-sigs>`__
      开发的 ``kubectl`` 插件管理器。
      具体安装方法请参阅 ``krew``
      `installation documentation <https://krew.sigs.k8s.io/docs/user-guide/setup/install/>`__。
      Krew 适用于 Linux、macOS 和 Windows 操作系统。

      你可以使用以下命令，通过 Krew 安装 MinIO ``kubectl`` 插件：

      .. code-block:: shell
         :class: copyable

         kubectl krew update
         kubectl krew install minio

      如果要通过 Krew 更新 MinIO 插件，请使用以下命令：

      .. code-block:: shell
         :class: copyable

         kubectl krew upgrade minio

   .. tab-item:: Manual (Linux, MacOS)

      你可以将 MinIO ``kubectl`` 插件下载到本地系统路径中。
      ``kubectl`` CLI 会自动发现并运行兼容插件。

      以下代码会下载最新版本的 MinIO Kubernetes 插件，
      并将其安装到系统路径中：

      .. code-block:: shell
         :substitutions:
         :class: copyable

         curl https://github.com/minio/operator/releases/download/v5.0.14/kubectl-minio_5.0.14_linux_amd64 -o kubectl-minio
         chmod +x kubectl-minio
         mv kubectl-minio /usr/local/bin/

      上述 ``mv`` 命令可能需要 ``sudo`` 提权，
      具体取决于当前认证用户的权限。

      运行以下命令验证插件是否安装成功：

      .. code-block:: shell
         :class: copyable

         kubectl minio version

      输出应显示 Operator 版本为 5.0.14。

   .. tab-item:: Manual (Windows)

      你可以将 MinIO ``kubectl`` 插件下载到本地系统路径中。
      ``kubectl`` CLI 会自动发现并运行兼容插件。

      以下 PowerShell 命令会下载最新版本的 MinIO Kubernetes 插件，
      并将其安装到系统路径中：

      .. code-block:: powershell
         :substitutions:
         :class: copyable

         Invoke-WebRequest -Uri "https://github.com/minio/operator/releases/download/v5.0.14/kubectl-minio_5.0.14_windows_amd64.exe" -OutFile "C:\kubectl-plugins\kubectl-minio.exe"

      请确保插件目录路径已包含在 Windows PATH 中。

      运行以下命令验证插件是否安装成功：

      .. code-block:: shell
         :class: copyable

         kubectl minio version

      输出应显示 Operator 版本为 5.0.14。
