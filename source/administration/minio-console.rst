.. _minio-console:

=============
MinIO Console
=============

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2


MinIO Console 是一个功能丰富的图形用户界面，提供与 :mc:`mc` 命令行工具类似的功能。

.. image:: /images/minio-console/minio-console.png
   :width: 600px
   :alt: MinIO Console 登录页为已认证用户提供 Object Browser 视图
   :align: center

本页面概述了 MinIO Console，并说明其配置选项和登录方法。

概述
----

您可以使用 MinIO Console 执行多种管理任务，例如身份与访问管理、指标与日志监控或服务器配置。

MinIO Console 作为 MinIO Server 的一部分内嵌提供。
您也可以按照 :minio-git:`github repository <console>` 中的说明部署独立的 MinIO Console。

支持的浏览器
~~~~~~~~~~~~

MinIO Console 可运行于多种当前稳定版本的浏览器。

为获得最佳使用体验，请使用您所选浏览器的最新稳定版本。
支持的浏览器包括：

- Chrome
- Edge
- Safari
- Firefox
- Opera

此列表 *并不完整*，后续可能发生变化。

如需查看运行 MinIO Console 所支持的完整浏览器及版本列表，请参见 `Browserslist <https://browsersl.ist/#q=%3E0.2%25%2Cnot+dead+and+not+op_mini+all>`__ 网站。

.. tip:: 
   
   MinIO Console *不支持* Opera Mini。

配置
----

MinIO Console 的大部分配置设置继承自 MinIO Server。以下环境变量用于启用
MinIO Console 中的特定行为：

.. list-table::
   :header-rows: 1
   :widths: 30 70
   :width: 100%

   * - 环境变量
     - 说明

   * - :envvar:`MINIO_PROMETHEUS_URL`
     - 配置为从 MinIO 部署抓取指标的 Prometheus 服务器 URL。
       MinIO Console 使用该服务器填充指标仪表板。

       有关如何配置 Prometheus 以从 MinIO 收集指标的教程，请参见
       :ref:`minio-metrics-collect-using-prometheus`。

   * - :envvar:`MINIO_BROWSER_REDIRECT_URL`
     - MinIO Console 对外可解析的主机名，供已配置的
       :ref:`external identity manager
       <minio-authentication-and-identity-management>` 返回认证响应时使用。

       使用反向代理、负载均衡器或类似系统将 MinIO Console 暴露到公网时，
       通常需要设置该变量。请指定一个可从外部访问并解析到 MinIO Console
       的主机名。

.. _minio-console-port-assignment:

静态端口分配与动态端口分配
~~~~~~~~~~~~~~~~~~~~~~~~~~

默认情况下，MinIO 会在每次服务器启动时为 MinIO Console 随机选择一个端口。
访问 MinIO Server 的浏览器客户端会被自动重定向到动态选定端口上的
MinIO Console。此行为模拟了旧版 Web 浏览器访问方式，同时降低了在
嵌入式 Console 更新前已运行 MinIO 的系统上发生端口冲突的风险。

您可以在启动部署中的每个 MinIO Server 时，通过传递
:mc-cmd:`minio server --console-address` 命令行选项来显式指定静态端口。

例如，以下命令启动一个分布式 MinIO 部署，并为 MinIO Console 静态分配
``9001`` 端口。该部署将在默认 MinIO 服务器端口 ``:9000`` 上响应
S3 API 操作，并在 MinIO Console 端口 ``:9001`` 上响应浏览器访问。

.. code-block:: shell
   :class: copyable

   minio server https://minio-{1...4}.example.net/mnt/drive-{1...4} \
         --console-address ":9001"

位于网络路由组件之后且其路由规则要求使用静态端口的部署，可能需要为
MinIO Console 设置静态端口。例如，负载均衡器、反向代理或 Kubernetes
ingress 可能会默认阻止动态重定向行为，或在该行为下表现异常。

您还必须确保主机系统防火墙允许访问已配置的 Console 端口。

.. _minio-console-play-login:

登录
----

.. versionchanged:: RELEASE.2023-03-09T23-16-13Z

MinIO Console 会向未认证用户显示登录页面。
默认情况下，Console 为 :ref:`MinIO-managed user <minio-internal-idp>` 提供用户名和密码登录提示。

对于配置了多个 :ref:`identity managers <minio-authentication-and-identity-management>` 的部署，
请选择 :guilabel:`Other Authentication Methods` 下拉菜单，以选择其他已配置的身份提供商之一。
您也可以使用通过 :ref:`Security Token Service (STS) <minio-security-token-service>` API 生成的凭证登录。

.. admonition:: 使用 MinIO 的 Play 测试环境体验 Console
   :class: note

   您可以通过 https://play.min.io:9443 体验 Console。
   请使用以下凭证登录：

   - 用户名：``Q3AM3UQ867SPQQA43P2F``
   - 密码：``zuf+tfteSlswRu7BJ86wekitnifILbZam1KYY3TG``

   Play Console 连接到位于 https://play.min.io 的 MinIO Play 部署。
   您也可以通过 :mc:`mc` 使用 ``play`` 别名访问该部署。

文档
----

:guilabel:`Documentation` 选项卡会在单独的浏览器窗口或标签页中打开此文档站点。

可用任务
--------

登录 MinIO Console 后，用户可以执行多种任务。

- :ref:`管理对象 <minio-console-managing-objects>`，包括浏览或上传对象、管理存储桶设置或创建层。
- :ref:`查看或修改身份与安全配置 <minio-console-security-access>`，包括访问密钥、策略和身份提供商设置。
- :ref:`监控健康状态与活动 <minio-console-managing-deployment>`，包括指标、通知或站点复制
- :ref:`管理部署许可证 <minio-console-subscription>`

.. toctree::
   :titlesonly:
   :hidden:

   /administration/console/managing-deployment
   /administration/console/managing-objects
   /administration/console/security-and-access
   /administration/console/subnet-registration
