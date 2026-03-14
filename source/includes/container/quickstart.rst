.. _quickstart-container:

.. |OS| replace:: Docker or Podman

本流程会在 |OS| 上部署一个 :ref:`单机单盘 <minio-installation-comparison>` MinIO server，
用于 MinIO 对象存储及其 S3 兼容 API 层的早期开发和评估。

如需面向生产环境的部署说明，请参阅 :ref:`deploy-minio-distributed`。

前提条件
--------

- 已安装 `Podman <https://podman.io/getting-started/installation.html>`_ 或 `Docker <https://docs.docker.com/get-docker/>`_。
- 对用于持久卷的文件夹或驱动器具有读、写和删除权限。

流程
----
   
#. 启动容器
   
   选择容器类型以查看创建容器的说明。
   GNU/Linux、MacOS 和 Windows 都提供了对应指引。

   .. dropdown:: Podman (Rootfull 或 Rootless)
      :name: podman-root-rootless
   
      以下步骤同时适用于 rootfull 和 rootless 容器。

      .. tab-set::
   
         .. tab-item:: GNU/Linux 或 MacOS
   
            .. code-block:: shell
               :class: copyable

               mkdir -p ~/minio/data

               podman run \
                  -p 9000:9000 \
                  -p 9001:9001 \
                  -v ~/minio/data:/data \
                  -e "MINIO_ROOT_USER=ROOTNAME" \
                  -e "MINIO_ROOT_PASSWORD=CHANGEME123" \
                  quay.io/minio/minio server /data --console-address ":9001"
   
            上述示例的工作方式如下：
   
            - ``podman run`` 用于启动容器。
              该进程会附着在当前终端会话上，退出终端时进程也会结束。
            - ``-p`` 将本地端口绑定到容器端口。
            - ``-v`` 为容器指定一个文件路径作为持久卷位置。
              当 MinIO 向 ``/data`` 写入数据时，这些数据会映射到本地路径 ``~/minio/data``，
              因而可以在容器重启后保留。
              你可以改用任意用户具有读、写和删除权限的文件路径。
            - ``-e`` 分别设置环境变量 :envvar:`MINIO_ROOT_USER`
              和 :envvar:`MINIO_ROOT_PASSWORD`。
              它们用于设置 :ref:`root user credentials <minio-users-root>`。
              请将示例值替换为你自己的容器配置值。
   
         .. tab-item:: Windows
   
            .. code-block:: shell
               :class: copyable
   
               podman run \
                  -p 9000:9000 \
                  -p 9001:9001 \
                  -v D:\minio\data:/data \
                  -e "MINIO_ROOT_USER=ROOTNAME" \
                  -e "MINIO_ROOT_PASSWORD=CHANGEME123" \
                  quay.io/minio/minio server /data --console-address ":9001"
   
            上述示例的工作方式如下：
   
            - ``podman run`` 用于启动容器。
            - ``-p`` 将本地端口绑定到容器端口。
            - ``-v`` 为容器指定一个文件路径作为持久卷位置。
              当 MinIO 向 ``/data`` 写入数据时，这些数据会映射到本地路径 ``D:\minio\data``，
              因而可以在容器重启后保留。
              你可以改用任意用户具有读、写和删除权限的文件路径。
            - ``-e`` 分别设置环境变量 :envvar:`MINIO_ROOT_USER`
              和 :envvar:`MINIO_ROOT_PASSWORD`。
              它们用于设置 :ref:`root user credentials <minio-users-root>`。
              请将示例值替换为你自己的容器配置值。
   
   .. dropdown:: Docker (Rootfull)
      :name: docker-rootfull
   
      .. tab-set::
   
         .. tab-item:: GNU/Linux 或 MacOS
   
            .. code-block:: shell
               :class: copyable
   
               mkdir -p ~/minio/data
   
               docker run \
                  -p 9000:9000 \
                  -p 9001:9001 \
                  --name minio \
                  -v ~/minio/data:/data \
                  -e "MINIO_ROOT_USER=ROOTNAME" \
                  -e "MINIO_ROOT_PASSWORD=CHANGEME123" \
                  quay.io/minio/minio server /data --console-address ":9001"
         
            上述示例的工作方式如下：
   
            - ``mkdir`` 会在你的家目录中创建本地目录 ``~/minio/data``。
            - ``docker run`` 用于启动 MinIO 容器。
            - ``-p`` 将本地端口绑定到容器端口。
            - ``-name`` 为容器创建名称。
            - ``-v`` 为容器指定一个文件路径作为持久卷位置。
              当 MinIO 向 ``/data`` 写入数据时，这些数据会映射到本地路径 ``~/minio/data``，
              因而可以在容器重启后保留。
              你可以将 ``~/minio/data`` 替换为任意其他本地文件位置，只要用户对此位置具有读、写和删除权限。
            - ``-e`` 分别设置环境变量 :envvar:`MINIO_ROOT_USER`
              和 :envvar:`MINIO_ROOT_PASSWORD`。
              它们用于设置 :ref:`root user credentials <minio-users-root>`。
              请将示例值替换为你自己的容器配置值。
                 
         .. tab-item:: Windows
   
            .. code-block:: shell
               :class: copyable
   
               docker run \
                  -p 9000:9000 \
                  -p 9001:9001 \
                  --name minio1 \
                  -v D:\minio\data:/data \
                  -e "MINIO_ROOT_USER=ROOTUSER" \
                  -e "MINIO_ROOT_PASSWORD=CHANGEME123" \
                  quay.io/minio/minio server /data --console-address ":9001"
               
            上述示例的工作方式如下：
   
            - ``docker run`` 用于启动 MinIO 容器。
            - ``-p`` 将本地端口绑定到容器端口。
            - ``-v`` 为容器指定一个文件路径作为持久卷位置。
              当 MinIO 向 ``/data`` 写入数据时，这些数据会映射到本地路径 ``D:\minio\data``，
              因而可以在容器重启后保留。
              你可以将 ``D:\minio\data`` 替换为任意其他本地文件位置，只要用户对此位置具有读、写和删除权限。
            - ``-e`` 分别设置环境变量 :envvar:`MINIO_ROOT_USER`
              和 :envvar:`MINIO_ROOT_PASSWORD`。
              它们用于设置 :ref:`root user credentials <minio-users-root>`。
              请将示例值替换为你自己的容器配置值。
            
   .. dropdown:: Docker (Rootless)
      :name: docker-rootless
   
      .. tab-set::
   
         .. tab-item:: GNU/Linux 或 MacOS
   
            .. code-block:: shell
               :class: copyable
   
               mkdir -p ${HOME}/minio/data
   
               docker run \
                  -p 9000:9000 \
                  -p 9001:9001 \
                  --user $(id -u):$(id -g) \
                  --name minio1 \
                  -e "MINIO_ROOT_USER=ROOTUSER" \
                  -e "MINIO_ROOT_PASSWORD=CHANGEME123" \
                  -v ${HOME}/minio/data:/data \
                  quay.io/minio/minio server /data --console-address ":9001"
         
            上述示例的工作方式如下：
   
            - ``mkdir`` 会在你的家目录中创建本地目录 ``~/minio/data``。
            - ``docker run`` 用于启动 MinIO 容器。
            - ``-p`` 将本地端口绑定到容器端口。
            - ``-user`` 将容器中的用户名设置为当前用户及其用户组对应的策略。
            - ``-name`` 为容器创建名称。
            - ``-v`` 为容器指定一个文件路径作为持久卷位置。
              当 MinIO 向 ``/data`` 写入数据时，这些数据实际上会写入本地路径 ``~/minio/data``，
              因而可以在容器重启后保留。
              你可以将 ``${HOME}/minio/data`` 替换为用户家目录中的其他位置，只要用户对此位置具有读、写和删除权限。
            - ``-e`` 分别设置环境变量 :envvar:`MINIO_ROOT_USER`
              和 :envvar:`MINIO_ROOT_PASSWORD`。
              它们用于设置 :ref:`root user credentials <minio-users-root>`。
              请将示例值替换为你自己的容器配置值。
                 
         .. tab-item:: Windows

            前提条件：

            - 已定义 Windows `Group Managed Service Account <https://docs.microsoft.com/en-us/virtualization/windowscontainers/manage-containers/manage-serviceaccounts>`_。
   
            .. code-block:: shell
               :class: copyable
   
               docker run \
                  -p 9000:9000 \
                  -p 9001:9001 \
                  --name minio1 \
                  --security-opt "credentialspec=file://path/to/file.json"
                  -e "MINIO_ROOT_USER=ROOTUSER" \
                  -e "MINIO_ROOT_PASSWORD=CHANGEME123" \
                  -v D:\data:/data \
                  quay.io/minio/minio server /data --console-address ":9001"
   
            上述示例的工作方式如下：
   
            - ``docker run`` 用于启动 MinIO 容器。
            - ``-p`` 将本地端口绑定到容器端口。
            - ``-name`` 为容器创建名称。
            - ``--security-opt`` 通过 ``credentialspec`` 文件为容器授予
              `Group Managed Service Account (gMSA) <https://docs.microsoft.com/en-us/virtualization/windowscontainers/manage-containers/gmsa-run-container>`_
              访问能力。
            - ``-v`` 为容器指定一个文件路径作为持久卷位置。
              当 MinIO 向 ``/data`` 写入数据时，这些数据实际上会写入本地路径 ``D:\data``，
              因而可以在容器重启后保留。
              你可以将 ``D:\data`` 替换为任意其他本地文件位置，只要用户对此位置具有读、写和删除权限。
            - ``-e`` 分别设置环境变量 :envvar:`MINIO_ROOT_USER`
              和 :envvar:`MINIO_ROOT_PASSWORD`。
              它们用于设置 :ref:`root user credentials <minio-users-root>`。
              请将示例值替换为你自己的容器配置值。

#. 将浏览器连接到 MinIO 服务端

   打开浏览器并访问 ``http://127.0.0.1:9000``，
   或者访问 :mc:`minio server` 命令输出中列出的任一 Console 地址，即可进入 :ref:`minio-console`。
   例如，示例输出中的 :guilabel:`Console: http://192.0.2.10:9001 http://127.0.0.1:9001`
   就表示有两个可用于连接 Console 的地址。

   虽然端口 ``9000`` 用于连接 API，
   但 MinIO 会自动将浏览器访问重定向到 MinIO Console。

   使用你在 :envvar:`MINIO_ROOT_USER` 和 :envvar:`MINIO_ROOT_PASSWORD`
   环境变量中定义的凭证登录 Console。
   每个 MinIO server 都内置自己的 MinIO Console。

#. `(Optional)` 安装 MinIO Client

   :ref:`MinIO Client <minio-client>` 允许你在命令行中操作 MinIO 存储卷。

   请选择对应操作系统查看说明。

   .. dropdown:: GNU/Linux

      :ref:`MinIO Client <minio-client>` 允许你在命令行中操作 MinIO server。

      下载 :mc:`mc` 客户端，并将其安装到系统 ``PATH`` 中的某个位置，
      例如 ``/usr/local/bin``。
      你也可以直接在下载目录运行该二进制文件。

      .. code-block:: shell
         :class: copyable
         
         wget https://dl.min.io/client/mc/release/linux-amd64/mc
         chmod +x mc
         sudo mv mc /usr/local/bin/mc
   
      使用 :mc:`mc alias set` 创建与本地部署关联的新别名。
      之后你就可以针对该别名执行 :mc:`mc` 命令：

      .. code-block:: shell
         :class: copyable
      
         mc alias set local http://127.0.0.1:9000 {MINIO_ROOT_USER} {MINIO_ROOT_PASSWORD}
         mc admin info local

      将 ``{MINIO_ROOT_USER}`` 和 ``{MINIO_ROOT_PASSWORD}``
      替换为你通过 ``-e`` 参数为容器定义的凭证。
      
      :mc:`mc alias set` 需要四个参数：
   
      - 别名名称
      - MinIO server 的主机名或 IP 地址及端口
      - MinIO :ref:`user <minio-users>` 的 Access Key
      - MinIO :ref:`user <minio-users>` 的 Secret Key

      关于该命令的更多细节，请参阅 :ref:`alias`。

   .. dropdown:: MacOS
      
      :ref:`MinIO Client <minio-client>` 允许你在命令行中操作 MinIO 存储卷。
      
      .. tab-set::
      
         .. tab-item:: Homebrew
      
            运行以下命令，使用 `Homebrew <https://brew.sh>`_ 安装最新稳定版 MinIO Client 软件包。
      
            .. code-block:: shell
               :class: copyable
      
               brew install minio/stable/mc

         .. tab-item:: Binary (arm64)
      
            运行以下命令，使用适用于 Apple 芯片的二进制软件包安装最新稳定版 MinIO Client。

            .. code-block:: shell
               :class: copyable

               curl -O https://dl.min.io/client/mc/release/darwin-arm64/mc
               chmod +x mc
               sudo mv mc /usr/local/bin/mc

         .. tab-item:: Binary (amd64)
                       
            运行以下命令，使用适用于 Intel 芯片的二进制软件包安装最新稳定版 MinIO Client。

            .. code-block:: shell
               :class: copyable

               curl -O https://dl.min.io/client/mc/release/darwin-amd64/mc
               chmod +x mc
               sudo mv mc /usr/local/bin/mc
      
      使用 :mc:`mc alias set` 快速完成认证并连接到 MinIO 部署。
      
      .. code-block:: shell
         :class: copyable
      
         mc alias set local http://127.0.0.1:9000 {MINIO_ROOT_USER} {MINIO_ROOT_PASSWORD}
         mc admin info local
      
      将 ``{MINIO_ROOT_USER}`` 和 ``{MINIO_ROOT_PASSWORD}``
      替换为你通过 ``-e`` 参数为容器定义的凭证。

      :mc:`mc alias set` 需要四个参数：
   
      - 别名名称
      - MinIO server 的主机名或 IP 地址及端口
      - MinIO :ref:`user <minio-users>` 的 Access Key
      - MinIO :ref:`user <minio-users>` 的 Secret Key

      关于该命令的更多细节，请参阅 :ref:`alias`。
      
   .. dropdown:: Windows
   
      通过以下链接下载适用于 Windows 的独立 MinIO Client：
   
      https://dl.min.io/client/mc/release/windows-amd64/mc.exe
   
      双击文件即可运行。
      或者在 Command Prompt 或 PowerShell 中运行以下命令。
      
      .. code-block::
         :class: copyable
   
         \path\to\mc.exe --help
         
      使用 :mc:`mc alias set` 快速完成认证并连接到 MinIO 部署。
   
      .. code-block:: shell
         :class: copyable
   
         mc.exe alias set local http://127.0.0.1:9000 {MINIO_ROOT_USER} {MINIO_ROOT_PASSWORD}
         mc.exe admin info local
   
      将 ``{MINIO_ROOT_USER}`` 和 ``{MINIO_ROOT_PASSWORD}``
      替换为你通过 ``-e`` 参数为容器定义的凭证。
      
      :mc:`mc alias set` 需要四个参数：
   
      - 别名名称
      - MinIO server 的主机名或 IP 地址及端口
      - MinIO :ref:`user <minio-users>` 的 Access Key
      - MinIO :ref:`user <minio-users>` 的 Secret Key

      关于该命令的更多细节，请参阅 :ref:`alias`。

.. rst-class:: section-next-steps

后续步骤
--------

- :ref:`将应用连接到 MinIO <minio-drivers>`
- :ref:`配置对象保留 <minio-object-retention>`
- :ref:`配置安全能力 <minio-authentication-and-identity-management>`
- :ref:`在分布式环境中部署 MinIO <deploy-minio-distributed>`
