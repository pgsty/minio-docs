MinIO 使用先更新后重启的方法将部署升级到较新版本：

1. 使用较新版本更新 MinIO 二进制文件。
2. 使用 :mc-cmd:`mc admin service restart` 重启部署。

该流程无需停机，也不会中断正在进行的操作。

本页介绍对 ``systemctl`` 管理的 MinIO 部署和用户自管 MinIO 部署使用
先更新后重启方法进行升级的方式。
使用 Ansible、Terraform 或其他管理工具的部署，
可以将此处流程作为在现有自动化框架中实施升级的参考。

前提条件
--------

先备份集群设置
~~~~~~~~~~~~~~

在开始执行此流程之前，使用 :mc:`mc admin cluster bucket export`
和 :mc:`mc admin cluster iam export` 命令对存储桶元数据和 IAM 配置进行快照。
如有需要，你可以使用这些快照恢复
:ref:`bucket <minio-mc-admin-cluster-bucket-import>` 和
:ref:`IAM <minio-mc-admin-cluster-iam-import>` 设置，
以便从用户或流程错误中恢复。

检查发行说明
~~~~~~~~~~~~

MinIO 发布 :minio-git:`Release Notes <minio/releases>` 供你参考，
以识别每个版本所引入的变更。
请查看当前 MinIO 版本与目标较新版本之间各版本对应的发行说明，
以完整了解所有变更。

尤其要注意任何 *不* 向后兼容的版本。
你无法轻易从这类版本回退。

在应用到生产环境前先测试升级
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

MinIO 在每个版本发布时都会使用测试和验证套件。
但任何测试套件都无法覆盖你生产环境中硬件、软件和工作负载的所有特定组合。

在将任何 MinIO 升级应用到生产部署或其他包含关键数据的环境之前，
你都应先在较低环境（Dev/QA/Staging）中完成验证。
如果不先在较低环境中验证就直接更新生产环境，风险由你自行承担。

对于明显落后于最新稳定版（6 个月以上）的 MinIO 部署，
建议在升级过程中使用 |SUBNET| 获取额外支持和指导。

注意事项
--------

升级不会中断业务
~~~~~~~~~~~~~~~~

MinIO 的先更新后重启流程 *不* 需要停机或安排维护窗口。
MinIO 重启速度很快，并行重启所有服务端进程通常只需几秒。
MinIO 操作具备原子性且严格一致，因此使用 MinIO 或 S3 SDK 的应用
可以依赖内置的
:aws-docs:`transparent retry <general/latest/gr/api-retries.html>`，
无需额外的客户端逻辑。
这可确保升级不会中断正在进行的操作。

.. _minio-upgrade-systemctl:

升级由 ``systemctl`` 管理的 MinIO 部署
--------------------------------------

使用以下步骤升级由 ``systemctl`` 管理 MinIO 服务进程的部署，
例如通过 MinIO
:ref:`DEB/RPM packages <deploy-minio-distributed-baremetal>` 创建的部署。

本流程假定你已在所有 MinIO 节点上设置
:envvar:`MINIO_CONFIG_ENV_FILE` 变量。

1. 在每个节点上更新 MinIO 二进制文件

   .. include:: /includes/linux/common-installation.rst
      :start-after: start-upgrade-minio-binary-desc
      :end-before: end-upgrade-minio-binary-desc

   在每个节点上运行 ``minio --version``，
   确认你已将所有二进制文件成功升级到相同版本。
   除非所有节点都使用相同的 MinIO 二进制版本，否则 **不要** 继续。

2. 重启部署

   运行 :mc-cmd:`mc admin service restart` 命令，
   同时重启部署中的所有 MinIO 服务进程。

   .. code-block:: shell
      :class: copyable

      mc admin service restart ALIAS

   将 ``ALIAS`` 替换为要重启的 MinIO 部署的 :ref:`别名 <alias>`。

   S3 兼容 SDK 和应用应自动重试操作，
   因此重启过程通常 *不会中断* 正在进行的操作。

3. 验证升级

   使用 :mc:`mc admin info` 命令检查所有 MinIO 服务器是否在线、运行正常，
   且显示已安装的 MinIO 版本。

4. 更新 MinIO Client

   你应升级 :mc:`mc` 二进制文件，使其与 MinIO server 版本一致或尽量接近。
   你可以使用 :mc:`mc update` 命令将该二进制文件更新到最新稳定版本：

   .. code-block:: shell
      :class: copyable

      mc update

.. _minio-upgrade-mc-admin-update:

升级非系统管理的 MinIO 部署
---------------------------

使用以下步骤升级不由系统（``systemd``、``systemctl``）管理 MinIO 服务进程的部署，
例如由用户、自动化脚本或其他进程管理工具管理的部署。
该流程仅适用于运行 MinIO 进程的用户对 MinIO 二进制文件路径具有写权限的系统。
对于使用 ``systemctl`` 管理的部署，请参阅 :ref:`minio-upgrade-systemctl`。

使用 ``mc admin update`` 更新
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

:mc:`mc admin update` 命令会先更新目标 MinIO 部署中的所有 MinIO server 二进制文件，
然后同时重启所有节点。
重启过程通常会在几秒内完成，且 *不会中断* 正在进行的操作。

以下命令会将具有指定 :ref:`别名 <alias>` 的 MinIO 部署
更新到最新稳定版本：

.. code-block:: shell
   :class: copyable

   mc admin update ALIAS

运行 ``mc admin update`` 命令的用户 **必须**
对二进制文件安装位置具有 ``write`` 权限。

你可以指定一个解析到特定 MinIO server 二进制版本的 URL。
气隙或与互联网隔离的部署可以利用此功能，
从内部可访问的服务器执行更新：

.. code-block:: shell
   :class: copyable

   mc admin update ALIAS https://minio-mirror.example.com/minio.sha256sum

你应升级 :mc:`mc` 二进制文件，使其与 MinIO server 版本一致或尽量接近。
你可以使用 :mc:`mc update` 命令将该二进制文件更新到最新稳定版本：

.. code-block:: shell
   :class: copyable

   mc update

通过手动替换二进制文件更新
~~~~~~~~~~~~~~~~~~~~~~~~~~

你可以在部署中的每个主机节点上下载并手动替换 ``minio`` server 二进制文件。
然后必须同时重启所有节点，例如使用 :mc-cmd:`mc admin service restart`。

例如，以下命令会下载适用于 Linux 的最新稳定版 MinIO 二进制文件，
并将其复制到 ``/usr/local/bin``。
该命令会覆盖该路径上现有的 ``minio`` 二进制文件。

.. code-block:: shell
   :class: copyable

   wget https://dl.min.io/server/minio/release/linux-amd64/minio
   chmod +x ./minio
   sudo mv -f ./minio /usr/local/bin/minio

在你替换完部署中所有 MinIO 主机上的二进制文件后，
必须同时重启所有节点。

你应升级 :mc:`mc` 二进制文件，使其与 MinIO server 版本一致或尽量接近。
你可以使用 :mc:`mc update` 命令将该二进制文件更新到最新稳定版本：

.. code-block:: shell
   :class: copyable

   mc update
