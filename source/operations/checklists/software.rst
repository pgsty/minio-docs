.. _minio-software-checklists:

============
软件检查清单
============

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

在为生产级分布式 MinIO 部署规划软件配置时，请使用以下检查清单。

MinIO 前置条件
--------------

.. list-table::
   :widths: auto
   :width: 100%

   * - :octicon:`circle`
     - 运行 Linux 操作系统且内核版本为 6.6+ 的服务器。Red Hat Enterprise Linux (RHEL) 10 或 Ubuntu LTS 22.04.01+ 默认提供这类内核。
       确保所选操作系统使用 LTS 且仍受支持的 6.6+ Linux 内核版本。

   * - :octicon:`circle`
     - 一种在节点之间同步时间的方法，例如 ``ntp``、``timedatectl`` 或 ``timesyncd``。
       具体使用哪种方法取决于操作系统。
       请查阅你的操作系统文档，了解如何与时间服务器同步时间。

   * - :octicon:`circle`
     - 禁用会对文件系统、系统级调用或内核级调用进行索引、扫描或审计的系统服务。
       这些服务可能因资源争用或拦截 MinIO 操作而降低性能。

       MinIO 强烈建议在运行 MinIO 的主机上卸载或禁用以下服务：

       - ``mlocate`` 或 ``plocate``
       - ``updatedb``
       - ``auditd``
       - Crowdstrike Falcon
       - 杀毒软件 (``clamav``)
      
       上述列表列出了在 MinIO 这类高性能系统上，最常见的、已知会引发性能或行为问题的服务或软件。
       对于在 MinIO 主机上功能与上述项目类似的其他服务或软件，也应考虑移除或禁用。

       或者，将这些服务配置为忽略或排除 MinIO Server 进程，以及 MinIO 访问的 *全部* 驱动器或驱动器路径。

   * - :octicon:`circle` 
     - 对远程服务器具有系统管理员访问权限

   * - :octicon:`circle`
     - 一种分布式系统管理工具，例如 Ansible、Terraform，或在编排环境中使用 Kubernetes。
       Kubernetes 基础设施应使用 MinIO Operator 以获得最佳效果。

   * - :octicon:`circle`
     - 用于处理请求路由的负载均衡器（例如 `NGINX <https://www.nginx.com/>`__）

   * - :octicon:`circle`
     - 用于监控和指标采集的 :ref:`Prometheus <minio-metrics-collect-using-prometheus>` 或兼容 Prometheus 的方案

   * - :octicon:`circle`
     - 已完成 :ref:`Grafana 配置 <minio-grafana>`，用于仪表板展示

   * - :octicon:`circle` 
     - （可选）在本地主机系统上安装 :mc:`mc`


安装 MinIO
----------

在部署中的所有节点上安装相同版本的 MinIO。

安装后任务
----------

.. list-table::
   :widths: auto
   :width: 100%

   * - :octicon:`circle` 
     - （可选）从本地机器使用 :mc:`mc alias set` 为每台服务器创建 :mc:`mc alias`，以便通过命令行从本地管理 MinIO 部署

   * - :octicon:`circle`
     - 配置 :ref:`存储桶复制 <minio-bucket-replication-requirements>`，将一个存储桶中的内容复制到另一个存储桶位置

   * - :octicon:`circle`
     - 配置 :ref:`站点复制 <minio-site-replication-overview>`，同步多个分散数据中心位置的内容

   * - :octicon:`circle`
     - 使用 :ref:`生命周期管理 <minio-lifecycle-management>` 配置对象保留规则，以管理对象何时过期

   * - :octicon:`circle`
     - 使用分层配置 :ref:`对象存储级别规则 <minio-lifecycle-management-tiering>`，在热、温、冷存储之间移动对象，以最大化存储成本效率

.. include:: /includes/common-admonitions.rst
   :start-after: start-exclusive-drive-access
   :end-before: end-exclusive-drive-access

第三方身份提供商任务
--------------------

.. list-table::
   :widths: auto
   :width: 100%

   * - :octicon:`circle`
     - | 使用 :ref:`Security Token Service (STS) <minio-security-token-service>` 对 MinIO 进行身份验证
       | 启用该功能需要 MinIO 支持。
