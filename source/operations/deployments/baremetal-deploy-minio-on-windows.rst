.. _deploy-minio-windows:

==========================
在 Windows 上部署 MinIO
==========================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 1

.. container:: extlinks-video

   - `Object Storage Essentials <https://www.youtube.com/playlist?list=PLFOIsHSSYIK3WitnqhqfpeZ6fRFKHxIr7>`__
   
   - `How to Connect to MinIO with JavaScript <https://www.youtube.com/watch?v=yUR4Fvx0D3E&list=PLFOIsHSSYIK3Dd3Y_x7itJT1NUKT5SxDh&index=5>`__

本页介绍如何在 Microsoft Windows 主机上部署 MinIO。

MinIO 正式支持处于 Microsoft Modern Lifecycle Policy“Active Support”阶段的 Windows 操作系统。

在撰写本文时，包括：

- Windows Server 23H2（**推荐**）
- Windows Server 2022 LTSC 
- Windows 11 Enterprise/Workstation 23H2
- Windows 11 Enterprise/Workstation 22H2
- Windows 10 Enterprise 21H2 (LTS)
- Windows 10 IoT 21H2 (LTS)
- Windows 10 Enterprise 22H2

MinIO *可能* 也能在更旧或已停止支持的 Windows 版本上运行，但 MinIO 或 Microsoft 仅能提供有限的支持或故障排查。

本步骤包含对 Single-Node Multi-Drive (SNMD) 和 Single-Node Single-Drive (SNSD) 拓扑的指导，适用于早期开发和评估环境。

MinIO 不正式支持在 Windows 主机上运行 Multi-Node Multi-Drive (MNMD)“Distributed”配置。

注意事项
--------

检查清单
~~~~~~~~

在执行本步骤前，请先阅读我们发布的硬件、软件和安全检查清单。

纠删码校验
~~~~~~~~~~

MinIO 会根据拓扑中的节点和驱动器总数，自动为集群确定默认的 :ref:`纠删码 <minio-erasure-coding>` 配置。
你可以在设置集群时配置按对象生效的 :term:`parity`，也可以让 MinIO 选择默认值（生产级集群默认为 ``EC:4``）。

校验值决定了对象可用性与磁盘存储占用之间的关系。
可使用 MinIO `Erasure Code Calculator <https://min.io/product/erasure-code-calculator>`__ 选择适合你集群的纠删码校验级别。

虽然你可以随时更改纠删码校验设置，但以既有校验值写入的对象 **不会** 自动更新为新的校验设置。

步骤
----

1. 下载 MinIO 二进制文件
~~~~~~~~~~~~~~~~~~~~~~~~

从以下 URL 下载 MinIO 可执行文件：

.. code-block:: shell
   :class: copyable

   https://dl.min.io/server/minio/release/windows-amd64/minio.exe
   
下一步包含运行该可执行文件的说明。
你不能通过资源管理器或双击文件来运行它。
相反，你需要通过命令调用该可执行文件来启动 server。

2. 启动 MinIO Server
~~~~~~~~~~~~~~~~~~~~


在 PowerShell 或命令提示符中，切换到可执行文件所在目录，或将 ``minio.exe`` 文件路径加入系统 ``$PATH``。

.. tab-set::

   .. tab-item:: Multi-Drive

      对于带有多个驱动器的 Windows 主机，你可以指定一组顺序驱动器，以便在 Single-Node Multi-Drive (SNMD) 拓扑中配置 MinIO：

      .. code-block::
         :class: copyable

         .\minio.exe server {D...G}:\minio --console-address :9001

      :mc:`minio server` 进程会将输出打印到系统控制台，类似如下：

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

      该进程绑定到当前 PowerShell 或命令提示符窗口。
      关闭窗口会停止 server 并结束该进程。

   .. tab-item:: Single-Drive

      使用此命令在 ``C:\minio`` 文件夹中启动本地 MinIO 实例。
      你可以将 ``C:\minio`` 替换为本地主机上的其他驱动器或文件夹路径。

      .. code-block::
         :class: copyable

         .\minio.exe server C:\minio --console-address :9001

      :mc:`minio server` 进程会将输出打印到系统控制台，类似如下：

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

      该进程绑定到当前 PowerShell 或命令提示符窗口。
      关闭窗口会停止 server 并结束该进程。

3. 使用浏览器连接到 MinIO Server
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

使用浏览器（例如 Microsoft Edge）访问 ``http://127.0.0.1:9001``，或访问 :mc:`minio server` 命令输出中列出的任意 Console 地址，以打开 :ref:`minio-console`。
例如，示例输出中的 ``Console: http://192.0.2.10:9001 http://127.0.0.1:9001`` 表示有两个可用于连接 Console 的地址。

尽管端口 ``9000`` 用于连接 API，MinIO 仍会自动将浏览器访问重定向到 MinIO Console。

使用输出中显示的 ``RootUser`` 和 ``RootPass`` 用户凭证登录 Console。
默认值为 ``minioadmin | minioadmin``。

.. image:: /images/minio-console/console-login.png
   :width: 600px
   :alt: MinIO Console 显示登录界面
   :align: center

你可以使用 MinIO Console 执行常规管理任务，例如身份与访问管理、指标和日志监控，或 Server 配置。
每个 MinIO server 都包含自身内嵌的 MinIO Console。

.. image:: /images/minio-console/minio-console.png
   :width: 600px
   :alt: MinIO Console 显示存储桶起始界面
   :align: center

更多信息请参阅 :ref:`minio-console` 文档。

4. `(Optional)` 安装 MinIO Client
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

:ref:`MinIO Client <minio-client>` 允许你从 PowerShell 与 MinIO 部署交互。

从以下链接下载 Windows 独立版 MinIO client：

https://dl.min.io/client/mc/release/windows-amd64/mc.exe

双击文件即可运行。
或者，在命令提示符或 PowerShell 中运行以下命令。

.. code-block::
   :class: copyable

   \path\to\mc.exe --help
   
使用 :mc:`mc.exe alias set <mc alias set>` 可以快速认证并连接到 MinIO 部署。

.. code-block:: shell
   :class: copyable

   mc.exe alias set local http://127.0.0.1:9000 minioadmin minioadmin
   mc.exe admin info local

:mc:`mc.exe alias set <mc alias set>` 需要四个参数：

- 别名名称
- MinIO server 的主机名或 IP 地址及端口
- MinIO :ref:`用户 <minio-users>` 的 Access Key
- MinIO :ref:`用户 <minio-users>` 的 Secret Key

有关此命令的更多细节，请参阅 :ref:`alias`。

5. 后续步骤
~~~~~~~~~~~

待补充
