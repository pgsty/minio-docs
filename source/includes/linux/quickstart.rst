.. _quickstart-linux:

.. |OS| replace:: Linux

本流程会在 |OS| 上部署一个 :ref:`Standalone <minio-installation-comparison>` MinIO server，
用于 MinIO 对象存储及其 S3 兼容 API 层的早期开发和评估。

如需面向生产环境的部署说明，请参阅 :ref:`deploy-minio-distributed`。

前提条件
--------

- 对本地用户目录（例如 ``~/minio``）具有读、写和执行权限。
- 具有将二进制安装到系统 ``PATH`` 的权限（例如可写入 ``/usr/local/bin``）。
- 熟悉 Linux terminal 或 shell（Bash、ZSH 等）。
- 使用 64 位 Linux 操作系统（例如 RHEL 8、Ubuntu LTS）。

流程
----

#. **安装 MinIO Server**

   .. include:: /includes/linux/common-installation.rst
      :start-after: start-install-minio-binary-desc
      :end-before: end-install-minio-binary-desc

#. **启动 MinIO Server**

   在系统 terminal 或 shell 中运行以下命令，
   使用 ``~/minio`` 文件夹启动本地 MinIO 实例。
   你也可以将该路径替换为本机上的其他文件夹路径：

   .. code-block:: shell
      :class: copyable

      mkdir ~/minio
      minio server ~/minio --console-address :9001

   ``mkdir`` 命令会在指定路径显式创建该文件夹。

   ``minio server`` 命令用于启动 MinIO server。
   路径参数 ``~/minio`` 指定了 server 使用的工作目录。

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

      WARNING: Detected default credentials 'minioadmin:minioadmin', we recommend that you change these values with 'MINIO_ROOT_USER' and 'MINIO_ROOT_PASSWORD' environment variables.

#. **将浏览器连接到 MinIO Server**

   在浏览器中打开 http://127.0.0.1:9000 即可访问 :ref:`MinIO Console <minio-console>`。
   你也可以使用 server 命令输出中列出的任意网络地址。
   例如，示例输出中的 :guilabel:`Console: http://192.0.2.10:9001 http://127.0.0.1:9001`
   就表示有两个可用于连接 Console 的地址。

   虽然端口 ``9000`` 用于连接 API，
   但 MinIO 会自动将浏览器访问重定向到 MinIO Console。

   使用输出中显示的 ``RootUser`` 和 ``RootPass`` 用户凭证登录 Console。
   它们默认是 ``minioadmin | minioadmin``。

#. `(Optional)` **安装 MinIO Client**

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
   之后你就可以针对该别名执行 :mc-cmd:`mc` 命令：

   .. code-block:: shell
      :class: copyable

      mc alias set local http://127.0.0.1:9000 minioadmin minioadmin
      mc admin info local

   :mc:`mc alias set` 需要四个参数：

   - 别名名称
   - MinIO server 的主机名或 IP 地址及端口
   - MinIO :ref:`user <minio-users>` 的 Access Key
   - MinIO :ref:`user <minio-users>` 的 Secret Key

   上述示例使用的是 :ref:`root user <minio-users-root>`。

.. rst-class:: section-next-steps

后续步骤
--------

- :ref:`将应用连接到 MinIO <minio-drivers>`
- :ref:`配置对象保留 <minio-object-retention>`
- :ref:`配置安全能力 <minio-authentication-and-identity-management>`
- :ref:`为生产环境部署 MinIO <deploy-minio-distributed>`
