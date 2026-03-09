.. tab-set::

   .. tab-item:: Linux

      以下命令会向系统 PATH 添加一个*临时*路径扩展，
      用于运行 ``minio`` 工具。
      如需永久修改系统 PATH，请遵循你的操作系统相关说明。

      另一种方式是进入下载目录，
      然后运行 ``./minio --help`` 来执行 ``minio``。

      **64 位 Intel**

      .. code-block:: shell
         :class: copyable

         curl https://dl.min.io/server/minio/release/linux-amd64/minio \
           --create-dirs \
           -o $HOME/minio-binaries/minio

         chmod +x $HOME/minio-binaries/minio
         export PATH=$PATH:$HOME/minio-binaries/

         minio --help

      **64 位 PPC**

      .. code-block:: shell
         :class: copyable

         curl https://dl.min.io/server/minio/release/linux-ppc64le/minio \
           --create-dirs \
           -o $HOME/minio-binaries/minio

         chmod +x $HOME/minio-binaries/minio
         export PATH=$PATH:$HOME/minio-binaries/

         minio --help

   .. tab-item:: macOS


      **Homebrew**

      .. code-block:: shell
         :class: copyable

         brew install minio/stable/minio
         minio --help

      **二进制下载**

      .. code-block:: shell
         :class: copyable

         curl https://dl.min.io/server/minio/release/darwin-amd64/minio \
           --create-dirs \
           -o $HOME/minio-binaries/minio

         chmod +x $HOME/minio-binaries/minio
         export PATH=$PATH:$HOME/minio-binaries/


   .. tab-item:: Windows

      在浏览器中打开以下文件：
      
      https://dl.min.io/server/minio/release/windows-amd64/minio.exe

      通过双击执行该文件，*或* 在命令提示符或 powershell 中运行以下命令：

      .. code-block:: powershell

         \path\to\mc.exe --help

   .. tab-item:: Source

      源码安装方式面向开发者和高级用户，
      并要求系统具备可正常使用的 Golang 环境。
      请参阅 `How to install Golang <https://golang.org/doc/install>`__。

      在终端环境中运行以下命令，从源码安装 ``minio``：

      .. code-block:: shell
         :class: copyable

         go install github.com/minio/minio@latest

      :mc-cmd:`mc admin update` 不支持源码安装方式。
