.. tab-set::

   .. tab-item:: Linux

      以下命令会向系统 PATH *临时* 追加一项，以便运行 ``mc`` 工具。
      若要永久修改系统 PATH，请遵循你的操作系统说明。

      或者，你也可以进入 ``mc`` 所在目录并运行 ``./mc --help``。

      **64 位 Intel**

      .. code-block:: shell
         :class: copyable

         curl https://dl.min.io/client/mc/release/linux-amd64/mc \
           --create-dirs \
           -o $HOME/minio-binaries/mc

         chmod +x $HOME/minio-binaries/mc
         export PATH=$PATH:$HOME/minio-binaries/

         mc --help

      **64 位 PPC**

      .. code-block:: shell
         :class: copyable

         curl https://dl.min.io/client/mc/release/linux-ppc64le/mc \
           --create-dirs \
           -o ~/minio-binaries/mc

         chmod +x $HOME/minio-binaries/mc
         export PATH=$PATH:$HOME/minio-binaries/

         mc --help
         
      **ARM64**

      .. code-block:: shell
         :class: copyable

         curl https://dl.min.io/client/mc/release/linux-arm64/mc \
           --create-dirs \
           -o ~/minio-binaries/mc

         chmod +x $HOME/minio-binaries/mc
         export PATH=$PATH:$HOME/minio-binaries/

         mc --help

      .. admonition:: 从 MinIO 下载页面安装
         :class: note

         MinIO 不会通过常见 Linux 软件仓库或包管理器
         （Ubuntu、RHEL、Archlinux/AUR）正式发布其二进制文件。
         MinIO 二进制文件唯一的官方来源是
         `MinIO Download Page <https://dl.min.io/client/mc/release/>`__。

         MinIO 不建议通过包管理器安装，因为上游仓库可能会安装错误的软件包，
         或安装被重命名的软件包。

         所有文档都假定你 *仅* 通过下载页面安装 *官方* ``mc`` 客户端二进制文件，
         且二进制名称未做任何修改。

   .. tab-item:: macOS

      .. code-block:: shell
         :class: copyable

         brew install minio/stable/mc
         mc --help


   .. tab-item:: Windows

      在浏览器中打开以下文件：
      
      https://dl.min.io/client/mc/release/windows-amd64/mc.exe

      双击该文件即可执行，*或者* 在命令提示符或 PowerShell 中运行以下命令：

      .. code-block:: powershell

         \path\to\mc.exe --help

   .. tab-item:: Source

      从源码安装适用于开发者和高级用户，并要求具备可用的 Golang 环境。请参阅
      `How to install Golang <https://golang.org/doc/install>`__.

      在终端环境中运行以下命令，从源码安装 ``mc``：

      .. code-block:: shell
         :class: copyable

         go install github.com/minio/mc@latest

      :mc:`mc update` 不支持源码安装方式。
