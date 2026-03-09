.. _deploy-minio-container:

=====================
以容器方式部署 MinIO
=====================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 1

本页说明如何在任何支持容器化进程的操作系统上以容器方式部署 MinIO。

本文档假定已安装 Docker、Podman 或其他支持标准容器镜像格式的类似 runtime。
MinIO 镜像使用 `Red Hat Universal Base Image 9 Micro <https://catalog.redhat.com/software/container-stacks/detail/609560d9e2b160d361d24f98>`__。

MinIO 容器的功能和性能可能会受到基础操作系统的限制。

本步骤包含对 Single-Node Multi-Drive (SNMD) 和 Single-Node Single-Drive (SNSD) 拓扑的指导，适用于早期开发和评估环境。

.. important::

   MinIO 通过 MinIO Kubernetes Operator 正式支持在 Kubernetes 基础设施上运行容器化的 Multi-Node Multi-Drive (MNMD)“Distributed”配置。

   MinIO 不支持，也不提供使用 Docker Swarm、Docker Compose 或其他编排容器环境部署分布式集群的说明。

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

容器存储
~~~~~~~~

本步骤假定你会将一个或多个专用存储设备挂载到容器中，作为 MinIO 的持久化存储。

存储在容器临时路径上的数据会在容器重启或删除时丢失。
使用此类路径的风险需自行承担。

步骤
----

1. 启动容器

本步骤提供 Podman 和 Docker 在 rootfull 模式下的说明。
对于 rootless 部署，请参考各 runtime 自身的文档完成配置和容器启动。

对于其他容器 runtime，请参阅对应文档，并使用等效的选项、参数或配置。

.. tab-set::

   .. tab-item:: Podman

      以下命令会先在你的主目录中创建一个文件夹，然后使用 Podman 启动 MinIO 容器：

      .. code-block:: shell
         :class: copyable

         mkdir -p ~/minio/data

         podman run \
            -p 9000:9000 \
            -p 9001:9001 \
            --name minio \
            -v ~/minio/data:/data \
            -e "MINIO_ROOT_USER=ROOTNAME" \
            -e "MINIO_ROOT_PASSWORD=CHANGEME123" \
            quay.io/minio/minio server /data --console-address ":9001"

      该命令分别将端口 ``9000`` 和 ``9001`` 绑定到 MinIO S3 API 和 Web Console。

      本地驱动器 ``~/minio/data`` 会挂载到容器内的 ``/data`` 目录。
      你可以按需修改 :envvar:`MINIO_ROOT_USER` 和 :envvar:`MINIO_ROOT_PASSWORD` 变量，以变更 root 登录信息。

      对于多驱动器部署，请将每个本地驱动器或其所在文件夹绑定到容器中按顺序编号的路径。
      然后修改 :mc:`minio server` 启动命令以指定这些路径：

      .. code-block:: shell
         :class: copyable

         mkdir -p ~/minio/data-{1..4}

         podman run \
            -p 9000:9000 \
            -p 9001:9001 \
            --name minio \
            -v /mnt/drive-1:/mnt/drive-1 \
            -v /mnt/drive-2:/mnt/drive-2 \
            -v /mnt/drive-3:/mnt/drive-3 \
            -v /mnt/drive-4:/mnt/drive-4 \
            -e "MINIO_ROOT_USER=ROOTNAME" \
            -e "MINIO_ROOT_PASSWORD=CHANGEME123" \
            quay.io/minio/minio server http://localhost:9000/mnt/drive-{1...4} --console-address ":9001"

      对于 Windows 主机，请使用 Windows 文件系统语义指定本地文件夹路径，例如 ``C:\minio\:/data``。

   .. tab-item:: Docker

      以下命令会先在你的主目录中创建一个文件夹，然后使用 Docker 启动 MinIO 容器：

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

      该命令分别将端口 ``9000`` 和 ``9001`` 绑定到 MinIO S3 API 和 Web Console。

      本地驱动器 ``~/minio/data`` 会挂载到容器内的 ``/data`` 目录。
      你可以按需修改 :envvar:`MINIO_ROOT_USER` 和 :envvar:`MINIO_ROOT_PASSWORD` 变量，以变更 root 登录信息。

      对于多驱动器部署，请将每个本地驱动器或其所在文件夹绑定到容器中按顺序编号的路径。
      然后修改 :mc:`minio server` 启动命令以指定这些路径：

      .. code-block:: shell
         :class: copyable

         mkdir -p ~/minio/data-{1..4}

         docker run \
            -p 9000:9000 \
            -p 9001:9001 \
            --name minio \
            -v /mnt/drive-1:/mnt/drive-1 \
            -v /mnt/drive-2:/mnt/drive-2 \
            -v /mnt/drive-3:/mnt/drive-3 \
            -v /mnt/drive-4:/mnt/drive-4 \
            -e "MINIO_ROOT_USER=ROOTNAME" \
            -e "MINIO_ROOT_PASSWORD=CHANGEME123" \
            quay.io/minio/minio server http://localhost:9000/mnt/drive-{1...4} --console-address ":9001"

      对于 Windows 主机，请使用 Windows 文件系统语义指定本地文件夹路径，例如 ``C:\minio\:/data``。

2. 连接到部署
~~~~~~~~~~~~~~~~

.. tab-set::

   .. tab-item:: 控制台

      在浏览器中打开 http://localhost:9000 以访问 :ref:`MinIO Console <minio-console>` 登录页。

      使用上一步中的 :guilabel:`MINIO_ROOT_USER` 和 :guilabel:`MINIO_ROOT_PASSWORD`
      进行登录。

      .. image:: /images/minio-console/console-login.png
         :width: 600px
         :alt: MinIO Console 登录页
         :align: center

      你可以使用 MinIO Console 执行常规管理任务，例如身份与访问管理、指标和日志监控，或 Server 配置。
      每个 MinIO server 都包含自身内嵌的 MinIO Console。

   .. tab-item:: CLI

      请按照本地主机上的 ``mc`` :ref:`安装说明 <mc-install>` 完成安装。
      运行 ``mc --version`` 验证安装结果。

      安装完成后，为该 MinIO 部署创建一个别名：

      .. code-block:: shell
         :class: copyable

         mc alias set myminio http://localhost:9000 USERNAME PASSWORD

      请根据你的部署修改主机名、用户名和密码。
