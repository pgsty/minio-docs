=====================================
MinIO 高性能对象存储
=====================================

.. default-domain:: minio

.. cond:: mindocs

   .. container:: extlinks-video

      - `在 Docker 上安装和运行 MinIO：概览 <https://youtu.be/mg9NRR6Js1s?ref=docs>`__
      - `在 Docker 上安装和运行 MinIO：安装实验 <https://youtu.be/Z0FtabDUPtU?ref=docs>`__
      - `对象存储基础 <https://www.youtube.com/playlist?list=PLFOIsHSSYIK3WitnqhqfpeZ6fRFKHxIr7>`__
      
      - `如何使用 JavaScript 连接到 MinIO <https://www.youtube.com/watch?v=yUR4Fvx0D3E&list=PLFOIsHSSYIK3Dd3Y_x7itJT1NUKT5SxDh&index=5>`__

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

MinIO 是一个 Kubernetes 原生、兼容 S3 的对象存储解决方案，旨在部署到应用所在的任何位置，包括本地环境、私有云、公有云和边缘基础设施。
MinIO 专为现代应用负载模式设计，以支持高性能分布式计算与 PB 级存储需求的结合。

本站点提供受支持平台上 MinIO 社区版对象存储部署的运维、管理和开发文档。

`English <https://silo.pigsty.io>`__ | `中文文档 <https://silo.pigsty.cc>`__ | `GitHub 仓库 <https://github.com/pgsty/minio>`__ | `Docker 镜像 <https://hub.docker.com/r/pgsty/minio>`__ | `Ansible 部署方案 <https://pigsty.cc/docs/minio>`__

.. important::

   **这是** `minio/docs <https://github.com/minio/docs>`__ **的社区维护分支，由** `Pigsty <https://pigsty.io>`__ **在** `pgsty/minio-docs <https://github.com/pgsty/minio-docs>`__ **中维护。**
   本项目 **并非** MinIO, Inc. 的关联项目，也未获得其认可，赞助与背书。
   “MinIO” 是 MinIO, Inc. 的商标，此处仅用于标识上游项目。

   与上游相比（`#1203 <https://github.com/minio/docs/commit/571f188>`__），变更极少：恢复了被移除的 :ref:`Console <minio-console>` 文档，并修复了失效链接。
   仍按原始的 `Creative Commons Attribution 4.0 International License <https://creativecommons.org/licenses/by/4.0/legalcode>`__ 发布。

快速开始
--------

.. tab-set::

   .. tab-item:: 沙盒

      MinIO 在 https://play.min.io 维护了一个社区服务器沙盒实例。
      你可以在本地系统上使用该实例进行实验或评估 MinIO。

      按照 :mc:`mc` CLI 的 :ref:`安装指南 <mc-install>` 在本地主机上安装该工具。

      :mc:`mc` 内置了一个预配置的 ``play`` 别名，用于连接该沙盒实例。
      例如，你可以使用以下命令创建存储桶并将对象复制到 ``play``：

      .. code-block:: shell
         :class: copyable

         mc mb play/mynewbucket

         mc cp /path/to/file play/mynewbucket/prefix/filename.extension

         mc stat play/mynewbucket/prefix/filename.extension

      .. important::

         MinIO Play 沙盒是一个临时性的公开部署，其访问凭证是众所周知的。
         上传到 Play 的任何私有、机密、内部、受保护或其他重要数据，实际上都会变成公开数据。
         请谨慎判断并自行承担上传到 Play 的数据风险。

   .. tab-item:: 裸金属

      1. 下载适用于你的操作系统的 MinIO 服务端进程

         按照 `MinIO 下载页面 <https://min.io/downloads?ref=docs>`__ 中与你的操作系统对应的说明，下载并安装 :mc:`minio server` 进程。

      2. 创建供 MinIO 使用的文件夹

         例如，在 Linux/MacOS 上创建 ``~/minio`` 文件夹，或在 Windows 上创建 ``C:\minio``。

      3. 启动 MinIO Server

         运行 :mc:`minio server`，指定目录路径以及 :mc:`~minio server --console-address` 参数，以设置静态的 Console 监听地址：

         .. code-block:: shell
            :class: copyable

            minio server ~/minio --console-address :9001
            # For windows, use minio.exe server ~/minio --console-address :9001`

         输出中会包含 :mc:`mc` 的连接说明，以及如何通过浏览器连接到 Console 的说明。

   .. tab-item:: Kubernetes

      将 `minio-dev.yaml <https://raw.githubusercontent.com/minio/docs/master/source/extra/examples/minio-dev.yaml>`__ 下载到你的主机：

      .. code-block:: shell
         :class: copyable

         curl https://raw.githubusercontent.com/minio/docs/master/source/extra/examples/minio-dev.yaml -O

      该文件描述了两个 Kubernetes 资源：

      - 一个新的命名空间 ``minio-dev``，以及
      - 一个使用 Worker 节点上的驱动器或卷来提供数据服务的 MinIO Pod

      使用 ``kubectl port-forward`` 访问该 Pod，或者为该 Pod 创建 Service，以便配置 Ingress、负载均衡或其他 Kubernetes 层网络功能。

.. toctree::
   :titlesonly:
   :hidden:

   /operations/deployments/installation
   /operations/replication/multi-site-replication
   /operations/concepts
   /operations/monitoring
   /operations/external-iam
   /operations/server-side-encryption
   /operations/network-encryption
   /operations/checklists
   /operations/data-recovery
   /operations/troubleshooting
   /administration/minio-console
   /administration/object-management
   /administration/monitoring
   /administration/identity-access-management
   /administration/server-side-encryption
   /administration/bucket-replication
   /administration/batch-framework
   /administration/concepts
   /developers/minio-drivers
   /developers/security-token-service
   /developers/transforms-with-object-lambda
   /developers/file-transfer-protocol
   /reference/kubernetes
   /reference/baremetal
   /reference/s3-api-compatibility
   /glossary
   /integrations/integrations
