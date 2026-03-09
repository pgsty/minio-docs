.. _deploy-minio-macos:

========================
在 MacOS 上部署 MinIO
========================

.. default-domain:: minio

.. container:: extlinks-video

   - `Object Storage Essentials <https://www.youtube.com/playlist?list=PLFOIsHSSYIK3WitnqhqfpeZ6fRFKHxIr7>`__
   
   - `How to Connect to MinIO with JavaScript <https://www.youtube.com/watch?v=yUR4Fvx0D3E&list=PLFOIsHSSYIK3Dd3Y_x7itJT1NUKT5SxDh&index=5>`__

本页介绍如何在 Apple MacOS 主机上部署 MinIO。

MinIO 正式支持仍处于服务期内的 MacOS 操作系统，该期限通常为自首次发布起 3 年。
在撰写本文时，包括：

- macOS 14 (Sonoma)（**推荐**）
- macOS 13 (Ventura)
- macOS 12 (Monterey) 

MinIO *可能* 也能在更旧或已停止支持的 macOS 版本上运行，但 MinIO 或 RedHat 仅能提供有限的支持或故障排查。

MinIO 同时支持基于 Intel 和 ARM 的 macOS 硬件，并为每种架构分别提供二进制文件。
请根据主机系统文档下载正确的二进制文件。

本步骤包含对 Single-Node Multi-Drive (SNMD) 和 Single-Node Single-Drive (SNSD) 拓扑的指导，适用于早期开发和评估环境。

MinIO 不正式支持在 MacOS 主机上运行 Multi-Node Multi-Drive (MNMD)“Distributed”配置。

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

.. tab-set::
   
      .. tab-item:: Homebrew

         打开终端，并运行以下命令使用 `Homebrew <https://brew.sh>`_ 安装最新稳定版 MinIO 软件包。

         .. code-block:: shell
            :class: copyable

            brew install minio/stable/minio

         .. important::

            如果你之前使用 ``brew install minio`` 安装过 MinIO server，建议改为从 ``minio/stable/minio`` 重新安装。

            .. code-block:: shell
               :class: copyable

               brew uninstall minio
               brew install minio/stable/minio

      .. tab-item:: Binary - arm64
         
         打开终端，然后使用以下命令下载最新稳定版 MinIO 二进制文件、赋予执行权限，并将其安装到系统 ``$PATH``：

            .. code-block:: shell
               :class: copyable

               curl -O https://dl.min.io/server/minio/release/darwin-arm64/minio
               chmod +x ./minio
               sudo mv ./minio /usr/local/bin/

      .. tab-item:: Binary - amd64
         
         打开终端，然后使用以下命令下载最新稳定版 MinIO 二进制文件、赋予执行权限，并将其安装到系统 ``$PATH``：

            .. code-block:: shell
               :class: copyable

               curl -O https://dl.min.io/server/minio/release/darwin-amd64/minio
               chmod +x ./minio
               sudo mv ./minio /usr/local/bin/

2. 启用 TLS 连接
~~~~~~~~~~~~~~~~

你可以跳过此步骤，以在未启用 TLS 的情况下部署。
MinIO 强烈 *不建议* 在早期开发之外的场景中进行非 TLS 部署。

为 MinIO 创建或提供 :ref:`传输层安全 (TLS) <minio-tls>` 证书，以自动启用 server 与客户端之间的 HTTPS 安全连接。

MinIO 要求私钥和公钥证书的默认文件名分别为 ``private.key`` 和 ``public.crt``。
请将证书放入专用目录：

.. code-block:: shell
   :class: copyable

   mkdir -p /opt/minio/certs

   cp private.key /opt/minio/certs
   cp public.crt /opt/minio/certs


MinIO 会根据操作系统/系统默认的受信任证书颁发机构列表来验证客户端证书。
若要启用对第三方证书或内部签发证书的验证，请将 CA 文件放入 ``/opt/minio/certs/CAs`` 目录。
CA 文件应包含从叶子证书到根证书的完整信任链，以确保验证成功。

有关为 MinIO 配置 TLS 的更具体指导，包括通过 Server Name Indication (SNI) 支持多域名，请参阅 :ref:`minio-tls`。

.. dropdown:: 早期开发环境证书

   对于本地测试或开发环境，你可以使用 MinIO :minio-git:`certgen <certgen>` 生成自签名证书。
   例如，以下命令会生成一组带有 IP 和 DNS Subject Alternate Names (SANs) 的自签名证书，这些 SAN 与 MinIO Server 主机关联：

   .. code-block:: shell

      certgen -host "localhost,minio-*.example.net"

   将生成的 ``public.crt`` 和 ``private.key`` 放入 ``/path/to/certs`` 目录，以为 MinIO 部署启用 TLS。
   应用程序可以将 ``public.crt`` 作为受信任的证书颁发机构，从而在不禁用证书校验的情况下连接到 MinIO 部署。

3. 创建 MinIO 环境文件
~~~~~~~~~~~~~~~~~~~~~~

在 ``/etc/default/minio`` 创建环境文件。
MinIO 服务将该文件作为 MinIO *以及* ``minio.service`` 文件所用全部 :ref:`环境变量 <minio-server-environment-variables>` 的来源。

请根据你的部署拓扑修改示例。

.. tab-set::

   .. tab-item:: Single-Node Multi-Drive

      在开发和评估环境中使用 Single-Node Multi-Drive 部署。
      对于能够容忍节点停机带来数据丢失或不可用的小型存储工作负载，也可以使用该拓扑。

      .. code-block:: shell
         :class: copyable

         # Set the volumes MinIO uses at startup
         # The command uses MinIO expansion notation {x...y} to denote a
         # sequential series. 
         # 
         # The following specifies a single host with 4 drives at the specified location
         #
         # The command includes the port that the MinIO server listens on
         # (default 9000).
         # If you run without TLS, change https -> http

         MINIO_VOLUMES="https://minio1.example.net:9000/mnt/drive{1...4}/minio"

         # Set all MinIO server command-line options
         #
         # The following explicitly sets the MinIO Console listen address to
         # port 9001 on all network interfaces. 
         # The default behavior is dynamic port selection.

         MINIO_OPTS="--console-address :9001 --certs-dir /opt/minio/certs"

         # Set the root username. 
         # This user has unrestricted permissions to perform S3 and 
         # administrative API operations on any resource in the deployment.
         #
         # Defer to your organizations requirements for superadmin user name.

         MINIO_ROOT_USER=minioadmin

         # Set the root password
         #
         # Use a long, random, unique string that meets your organizations
         # requirements for passwords.

         MINIO_ROOT_PASSWORD=minio-secret-key-CHANGE-ME

   .. tab-item:: Single-Node Single-Drive

      在早期开发和评估环境中使用 Single-Node Single-Drive（“Standalone”）部署。
      MinIO 不建议在生产环境中使用 Standalone 部署，因为节点或其存储介质丢失会导致数据丢失。

      .. code-block:: shell
         :class: copyable

         # Set the volume MinIO uses at startup
         # 
         # The following specifies the drive or folder path

         MINIO_VOLUMES="/mnt/drive1/minio"

         # Set all MinIO server command-line options
         #
         # The following explicitly sets the MinIO Console listen address to
         # port 9001 on all network interfaces. 
         # The default behavior is dynamic port selection.

         MINIO_OPTS="--console-address :9001 --certs-dir /opt/minio/certs"

         # Set the root username. 
         # This user has unrestricted permissions to perform S3 and 
         # administrative API operations on any resource in the deployment.
         #
         # Defer to your organizations requirements for superadmin user name.

         MINIO_ROOT_USER=minioadmin

         # Set the root password
         #
         # Use a long, random, unique string that meets your organizations
         # requirements for passwords.

         MINIO_ROOT_PASSWORD=minio-secret-key-CHANGE-ME

请根据部署需要，指定其他 :ref:`环境变量 <minio-server-environment-variables>` 或 server 命令行选项。

4. 启动 MinIO Server
~~~~~~~~~~~~~~~~~~~~

以下命令会启动附着在当前终端/shell 窗口上的 MinIO Server：

.. code-block:: shell
   :class: copyable

   export MINIO_CONFIG_ENV_FILE=/etc/default/minio
   minio server --console-address :9001

命令输出类似如下：

.. code-block:: shell

.. code-block:: shell

   MinIO Object Storage Server
   Copyright: 2015-2024 MinIO, Inc.
   License: GNU AGPLv3 - https://www.gnu.org/licenses/agpl-3.0.html
   Version: RELEASE.2024-06-07T16-42-07Z (go1.22.4 linux/amd64)

   API: https://minio-1.example.net:9000 https://203.0.113.10:9000 https://127.0.0.1:9000 
      RootUser: minioadmin 
      RootPass: minioadmin 

   WebUI: https://minio-1.example.net:9001 https://203.0.113.10:9001 https://127.0.0.1:9001          
      RootUser: minioadmin 
      RootPass: minioadmin 

   CLI: https://minio.pigsty.io/reference/minio-mc.html#quickstart
      $ mc alias set 'myminio' 'https://minio-1.example.net:9000' 'minioadmin' 'minioadmin'

   Docs: https://minio.pigsty.io/index.html
   Status:         1 Online, 0 Offline. 

``API`` 区块列出了客户端可访问 MinIO S3 API 的网络接口和端口。
``Console`` 区块列出了客户端可访问 MinIO Web Console 的网络接口和端口。

若要在后台运行 MinIO server 进程或以守护进程方式运行，请参阅 MacOS 操作系统文档中的最佳实践和操作步骤。

5. 连接到部署
~~~~~~~~~~~~~~

.. tab-set::

   .. tab-item:: Console

      打开浏览器，并通过任一 MinIO 主机名的 ``:9001`` 端口访问 :ref:`MinIO Console <minio-console>` 登录页。
      例如：``https://minio1.example.com:9001``。

      使用上一步中的 :guilabel:`MINIO_ROOT_USER` 和 :guilabel:`MINIO_ROOT_PASSWORD` 登录。

      .. image:: /images/minio-console/console-login.png
         :width: 600px
         :alt: MinIO Console 登录页
         :align: center

      你可以使用 MinIO Console 执行常规管理任务，例如身份与访问管理、指标和日志监控，或 Server 配置。
      每个 MinIO server 都包含自身内嵌的 MinIO Console。

   .. tab-item:: CLI

      请按照本地主机上的 ``mc`` :ref:`安装说明 <mc-install>` 完成安装。
      运行 ``mc --version`` 验证安装结果。

      如果你的 MinIO 部署使用第三方或自签名 TLS 证书，请将 :abbr:`CA (Certificate Authority)` 文件复制到 ``~/.mc/certs/CAs``，以便 ``mc`` 信任该证书链。

      安装完成后，为该 MinIO 部署创建一个别名：

      .. code-block:: shell
         :class: copyable

         mc alias set myminio https://minio-1.example.net:9000 USERNAME PASSWORD

      请根据你的部署修改主机名、用户名和密码。
      主机名可以是部署中的任意一个 MinIO 节点。
      你也可以指定负责处理部署连接的负载均衡器、反向代理或类似网络控制平面的主机名。

6. 后续步骤
~~~~~~~~~~~

待补充
