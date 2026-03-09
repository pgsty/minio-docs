.. _quickstart-windows:

.. |OS| replace:: Windows

本流程会在 |OS| 上部署一个 :ref:`Single-Node Single-Drive <minio-installation-comparison>` MinIO server，
用于 MinIO 对象存储及其 S3 兼容 API 层的早期开发和评估。

.. note::

   本文档仅涵盖 Single-Node Single-Drive 部署。
   由于 NTFS 的行为和限制，MinIO 不建议在 Windows 主机上进行多节点多驱动器部署。

   如需部署面向生产的分布式 MinIO，
   请使用 :minio-docs:`Linux hosts <minio/linux/operations/installation.html>`
   或 :minio-docs:`Kubernetes <minio/kubernetes/upstream/index.html>`。

Windows 版 MinIO 部署适用于早期开发和评估。
对于长期开发和生产环境，MinIO 强烈建议使用 Linux（RHEL、Ubuntu）系统。

MinIO 支持未达到 EOL 的 Windows 版本（Windows 10、Windows Server 2016+）。

前提条件
--------

- 对所选本地目录或文件路径具有读、写和执行权限
- 熟悉 Command Prompt 或 PowerShell 的使用

流程
----

#. 安装 MinIO Server

   从以下 URL 下载 MinIO 可执行文件：

   .. code-block:: shell
      :class: copyable

      https://dl.min.io/server/minio/release/windows-amd64/minio.exe
      
   下一步将说明如何运行该可执行文件。
   你不能在 Explorer 中或通过双击文件来运行它。
   正确方式是调用该可执行文件来启动 server。

#. 启动 :mc:`minio server`

   在 PowerShell 或 Command Prompt 中，
   进入该可执行文件所在位置，或者将 ``minio.exe`` 所在路径加入系统 ``$PATH``。
   
   使用以下命令在 ``C:\minio`` 文件夹中启动本地 MinIO 实例。
   你也可以将 ``C:\minio`` 替换为本机上的其他驱动器或文件夹路径。

   .. code-block::
      :class: copyable

      .\minio.exe server C:\minio --console-address :9001

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

      Documentation: https://minio.pigsty.io/

      WARNING: Detected default credentials 'minioadmin:minioadmin', we recommend that you change these values with 'MINIO_ROOT_USER' and 'MINIO_ROOT_PASSWORD' environment variables.
   
   该进程与当前 PowerShell 或 Command Prompt 窗口绑定。
   关闭窗口会停止 server，并终止该进程。

#. 将浏览器连接到 MinIO Server

   打开浏览器（例如 Microsoft Edge），访问 ``http://127.0.0.1:9001``，
   或者访问 :mc:`minio server` 命令输出中列出的任一 Console 地址，即可进入 :ref:`minio-console`。
   例如，示例输出中的 ``Console: http://192.0.2.10:9001 http://127.0.0.1:9001``
   就表示有两个可用于连接 Console 的地址。

   虽然端口 ``9000`` 用于连接 API，
   但 MinIO 会自动将浏览器访问重定向到 MinIO Console。

   使用输出中显示的 ``RootUser`` 和 ``RootPass`` 用户凭证登录 Console。
   它们默认是 ``minioadmin | minioadmin``。

#. `(Optional)` 安装 MinIO Client

   :ref:`MinIO Client <minio-client>` 允许你在命令行中操作 MinIO 存储卷。

   通过以下链接下载适用于 Windows 的独立 MinIO Client：

   https://dl.min.io/client/mc/release/windows-amd64/mc.exe

   双击文件即可运行。
   或者在 Command Prompt 或 PowerShell 中运行以下命令。
   
   .. code-block::
      :class: copyable

      \path\to\mc.exe --help
      
   使用 :mc:`mc.exe alias set <mc alias set>` 快速完成认证并连接到 MinIO 部署。

   .. code-block:: shell
      :class: copyable

      mc.exe alias set local http://127.0.0.1:9000 minioadmin minioadmin
      mc.exe admin info local

   :mc:`mc.exe alias set <mc alias set>` 需要四个参数：

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
