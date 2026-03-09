.. start-install-minio-binary-desc

下列选项卡给出了在 64 位 Linux 操作系统上使用 RPM、DEB 或二进制文件安装
MinIO 的示例。
RPM 和 DEB 软件包会自动将 MinIO 安装到所需的系统路径，并为 ``systemctl`` 创建
``minio`` 服务。
MinIO 强烈建议使用 RPM 或 DEB 安装方式。
如需更新通过 ``systemctl`` 管理的部署，请参见 :ref:`minio-upgrade`。

.. tab-set::

   .. tab-item:: RPM (RHEL)
      :sync: rpm

      使用以下命令下载并安装最新稳定版的 MinIO RPM。

      .. code-block:: shell
         :class: copyable
         :substitutions:

         wget |minio-rpm| -O minio.rpm
         sudo dnf install minio.rpm

   .. tab-item:: DEB (Debian/Ubuntu)
      :sync: deb

      使用以下命令下载并安装最新稳定版的 MinIO DEB：

      .. code-block:: shell
         :class: copyable
         :substitutions:

         wget |minio-deb| -O minio.deb
         sudo dpkg -i minio.deb

   .. tab-item:: 二进制文件
      :sync: binary

      使用以下命令下载最新稳定版的 MinIO 二进制文件，并将其安装到系统
      ``$PATH`` 中：

      .. code-block:: shell
         :class: copyable

         wget https://dl.min.io/server/minio/release/linux-amd64/minio
         chmod +x minio
         sudo mv minio /usr/local/bin/

.. end-install-minio-binary-desc

.. start-upgrade-minio-binary-desc

下列选项卡给出了在 64 位 Linux 操作系统上使用 RPM、DEB 或二进制可执行文件
更新 MinIO 的示例。

对于使用 Ansible 或 Terraform 等工具管理的基础设施，请遵循你们内部的流程，
在多个受管主机上更新软件包或二进制文件。

.. tab-set::

   .. tab-item:: RPM (RHEL)
      :sync: rpm

      使用以下命令下载最新稳定版的 MinIO RPM，并更新现有安装。

      .. code-block:: shell
         :class: copyable
         :substitutions:

         curl |minio-rpm| -O minio.rpm
         sudo dnf update minio.rpm

   .. tab-item:: DEB (Debian/Ubuntu)
      :sync: deb

      使用以下命令下载最新稳定版的 MinIO DEB，并升级现有安装：

      .. code-block:: shell
         :class: copyable
         :substitutions:

         curl |minio-deb| -O minio.deb
         sudo dpkg -i minio.deb

   .. tab-item:: 二进制文件
      :sync: binary

      使用以下命令下载最新稳定版的 MinIO 二进制文件，并覆盖现有二进制文件：

      .. code-block:: shell
         :class: copyable

         curl https://dl.min.io/server/minio/release/linux-amd64/minio
         chmod +x minio
         sudo mv minio /usr/local/bin/

      将 ``/usr/local/bin`` 替换为现有 MinIO 二进制文件所在的位置。
      如果尚不清楚路径，可运行 ``which minio`` 进行确认。

你可以通过计算每个二进制文件的 ``SHA256`` 校验和，并确认所有主机上的校验和
一致，来验证升级结果：

.. code-block:: shell
   :class: copyable

   shasum -a 256 /usr/local/bin/minio

:mc-cmd:`minio --version <minio server>` 的输出在所有主机上也应保持一致。

.. end-upgrade-minio-binary-desc

.. start-install-minio-tls-desc

当 MinIO 检测到 ``${HOME}/.minio/certs`` 目录中存在有效的 x.509 证书
（``.crt``）和私钥（``.key``）时，会自动启用
:ref:`传输层安全（TLS） <minio-tls>` 1.2+。

对于由 ``systemd`` 管理的部署，请使用运行 MinIO server 进程的用户对应的
``$HOME`` 目录。提供的 ``minio.service`` 文件会以 ``minio-user`` 身份运行
该进程。前一步已经包含了如何创建该用户及其主目录 ``/home/minio-user`` 的说明。

- 在每个主机上将 TLS 证书放置到 ``/home/minio-user/.minio/certs`` 中。

- 如果 *任何* MinIO server 或客户端使用了由未知证书颁发机构签名的证书
  （自签或内部 CA），你 *必须* 将 CA 证书放到该部署中所有 MinIO 主机的
  ``/home/minio-user/.minio/certs/CAs`` 下。MinIO 会拒绝无效证书
  （不受信任、已过期或格式错误）。

如果 ``minio.service`` 文件指定了其他用户账户，请改用该账户对应的 ``$HOME``
目录。或者，也可以通过 :mc-cmd:`minio server --certs-dir` 命令行参数指定
自定义证书目录。修改 ``/etc/default/minio`` 中的 ``MINIO_OPTS`` 变量即可设置
此选项。运行 MinIO server 进程的 ``systemd`` 用户 *必须* 对指定目录具有读取和
列目录权限。

有关为 MinIO 配置 TLS 的更具体指导，包括通过 Server Name Indication (SNI)
支持多域名，请参见 :ref:`minio-tls`。你也可以跳过这一步，以不启用 TLS 的方式
部署。除了早期开发阶段以外，MinIO 强烈 *不建议* 使用非 TLS 部署。

.. end-install-minio-tls-desc

.. start-install-minio-console-desc

打开浏览器，访问任意 MinIO 主机名的 ``:9001`` 端口，以打开
:ref:`MinIO Console <minio-console>` 登录页面。例如：
``https://minio1.example.com:9001``。

使用上一步中的 :guilabel:`MINIO_ROOT_USER` 和
:guilabel:`MINIO_ROOT_PASSWORD` 登录。

.. image:: /images/minio-console/console-login.png
   :width: 600px
   :alt: MinIO Console 登录页
   :align: center

每个 MinIO server 都内置了自己的 MinIO
Console。

.. end-install-minio-console-desc

.. start-local-jbod-single-node-desc

MinIO 强烈建议使用直连的 :abbr:`JBOD (Just a Bunch of Disks)` 阵列，并使用
XFS 格式化磁盘，以获得最佳性能。
使用任何其他类型的后端存储（SAN/NAS、ext4、RAID、LVM）通常都会降低性能、
可靠性、可预测性和一致性。

确保打算交由 MinIO 使用的所有 server 磁盘都属于相同类型（NVMe、SSD 或 HDD），
并且容量一致（例如 ``12`` TB）。
MinIO 不区分磁盘类型，也无法从混合存储类型中获益。
此外，MinIO 会将每块磁盘的可用容量限制为部署中最小磁盘的容量。
例如，如果部署中有 15 块 10TB 磁盘和 1 块 1TB 磁盘，MinIO 会将每块磁盘的容量
限制为 1TB。

MinIO *要求* 在创建新的 |deployment| 时使用扩展表示法 ``{x...y}`` 表示顺序连续的
磁盘序列，其中 |deployment| 中所有节点都挂载了完全相同的一组磁盘。
MinIO 还要求物理磁盘的顺序在重启前后保持不变，也就是说，某个挂载点必须始终指向
同一块已经格式化的磁盘。
因此，MinIO **强烈建议** 使用 ``/etc/fstab`` 或类似的基于文件的挂载配置，确保
重启后磁盘顺序不会发生变化。
例如：

.. code-block:: shell

   $ mkfs.xfs /dev/sdb -L DISK1
   $ mkfs.xfs /dev/sdc -L DISK2
   $ mkfs.xfs /dev/sdd -L DISK3
   $ mkfs.xfs /dev/sde -L DISK4

   $ nano /etc/fstab

     # <file system>  <mount point>  <type>  <options>         <dump>  <pass>
     LABEL=DISK1      /mnt/disk1     xfs     defaults,noatime  0       2
     LABEL=DISK2      /mnt/disk2     xfs     defaults,noatime  0       2
     LABEL=DISK3      /mnt/disk3     xfs     defaults,noatime  0       2
     LABEL=DISK4      /mnt/disk4     xfs     defaults,noatime  0       2

.. note:: 

   依赖挂载外部存储的云环境实例，如果一个或多个远程文件挂载返回错误或失败，
   可能会在启动时失败。
   例如，挂载了持久化 EBS 卷的 AWS ECS 实例，如果一个或多个 EBS 卷挂载失败，
   在使用标准 ``/etc/fstab`` 配置时可能无法启动。

   你可以设置 ``nofail`` 选项，以便在启动时静默挂载错误，并允许实例在存在一个或
   多个挂载问题的情况下继续启动。
   
   对于使用本地直连磁盘的系统，你不应使用这个选项，因为屏蔽磁盘错误会阻止
   MinIO 和操作系统以正常方式响应这些错误。

然后，你就可以使用扩展表示法 ``/mnt/disk{1...4}`` 指定整组磁盘。
如果你想在每块磁盘上使用特定子目录，则可指定为 ``/mnt/disk{1...4}/minio``。

MinIO **不** 支持将包含现有 MinIO 数据的磁盘任意迁移到新的挂载位置，无论这是
有意操作，还是由操作系统层面的行为导致。

.. end-local-jbod-single-node-desc

.. start-storage-requirements-desc

以下要求概括了 MinIO 硬件建议中的
:ref:`minio-hardware-checklist-storage` 一节：

使用本地存储
   直连存储（DAS）相比网络存储
   （:abbr:`NAS (Network Attached Storage)`、:abbr:`SAN (Storage Area Network)`、
   :abbr:`NFS (Network File Storage)`）在性能和一致性方面具有显著优势。
   对于主数据或“热”数据，MinIO 强烈建议使用闪存存储（NVMe、SSD）。

磁盘使用 XFS 格式
   MinIO 强烈建议为存储配置使用 XFS 格式化的磁盘。
   MinIO 在内部测试和验证套件中使用 XFS，因此对其在各种规模下的性能和行为更有信心。

   对于 EXT4、BTRFS 或 ZFS 等其他文件系统，MinIO **既不** 测试，也不推荐。

使用一致的磁盘类型
   MinIO 不区分磁盘类型，也无法从混合存储类型中获益。
   每个 :term:`pool` 都必须使用相同类型的磁盘（NVMe、SSD）

   例如，部署一个仅由 NVMe 磁盘组成的 pool。
   如果你在其中混用 SSD 或 HDD，MinIO 会将这些磁盘与 NVMe 磁盘一视同仁。
   这可能导致性能问题，因为某些磁盘的读写特性不同或更差，无法以与 NVMe 磁盘相同
   的速率响应。

使用一致的磁盘容量
   MinIO 会将每块磁盘的可用容量限制为 pool 中最小磁盘的容量。

   例如，部署一个由相同数量、容量均为 ``7.68TiB`` 的 NVMe 磁盘组成的 pool。
   如果其中有一块磁盘容量为 ``3.84TiB``，MinIO 会将 pool 中所有磁盘都视为只有
   该较小容量。

配置顺序编号的磁盘挂载
   MinIO 在创建新的 |deployment| 时使用 Go 扩展表示法 ``{x...y}`` 表示顺序连续的
   磁盘序列，其中 |deployment| 中所有节点都挂载了完全相同的一组磁盘。
   最好将磁盘挂载路径也配置成顺序序列，以便支持这种表示法。
   例如，可按 ``/mnt/drive-n`` 的模式挂载磁盘，其中 ``n`` 从 ``1`` 开始，并随每块
   磁盘按 ``1`` 递增。

在重启后保持磁盘挂载与映射一致
   使用 ``/etc/fstab`` 确保节点重启前后磁盘到挂载点的映射保持一致。

   非 Linux 操作系统应使用等效的磁盘挂载管理工具。

.. end-storage-requirements-desc

.. start-nondisruptive-upgrade-desc

MinIO 强烈建议同时重启一个部署中的所有 MinIO Server 进程。
MinIO 操作具有原子性并保持严格一致。
因此，该重启过程不会中断应用或正在进行的操作。

**不要** 执行“滚动”重启（例如一次只重启一个节点）。

.. end-nondisruptive-upgrade-desc

.. start-pool-order-must-not-change

.. admonition:: 先下线再新增时保持 pool 顺序
   :class: note

   如果你在多 pool 部署中下线了一个 pool，就不能在新 pool 中复用相同的节点编号序列。
   例如，假设某个部署包含以下几个 pool：

   .. code-block::

      https://minio-{1...4}.example.net/mnt/drive-{1...4}
      https://minio-{5...8}.example.net/mnt/drive-{1...4}
      https://minio-{9...12}.example.net/mnt/drive-{1...4}

   如果你下线了 ``minio-{5...8}`` 这个 pool，就不能再用相同的节点编号新增一个
   pool。你必须将新 pool 添加在 ``minio-{9...12}`` *之后*：

   .. code-block::

      https://minio-{1...4}.example.net/mnt/drive-{1...4}
      https://minio-{9...12}.example.net/mnt/drive-{1...4}
      https://minio-{13...16}.example.net/mnt/drive-{1...4}

.. end-pool-order-must-not-change
