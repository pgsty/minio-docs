.. _minio-metrics-and-alerts-endpoints:
.. _minio-metrics-and-alerts-alerting:
.. _minio-metrics-and-alerts:

============
指标与告警
============

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2


MinIO 使用 :prometheus-docs:`Prometheus 数据模型 <concepts/data_model/>` 发布指标。
你可以使用任意抓取工具从 MinIO 拉取指标数据，以执行进一步分析和配置告警。

从 MinIO 服务端 :minio-release:`RELEASE.2024-07-15T19-02-30Z` 与 MinIO 客户端 :mc-release:`RELEASE.2024-07-11T18-01-28Z` 开始，metrics version 3 提供了更多端点。
对于新部署，MinIO 建议使用 version 3。

.. admonition:: Metrics version 2
   :class: note

   现有部署可以继续使用 version 2 :ref:`指标 <minio-metrics-v2>` 和 :ref:`Grafana 仪表板 <minio-grafana>`。


Version 3 端点
--------------

对于 metrics version 3，所有指标都位于基础端点 ``/minio/metrics/v3`` 之下。
你可以抓取该基础端点以一次性收集全部指标，也可以追加可选路径，仅返回特定类别的指标。

.. important:: 

   本页中的 V3 指标说明可能存在缺漏、不准确或错误信息。
   如需最准确的指标定义，请参考 `minio/minio <https://github.com/minio/minio>`_ 仓库并审阅源代码。

例如，以下端点会返回 audit 指标：

.. code-block:: shell
   :class: copyable

   http://HOSTNAME:PORT/minio/metrics/v3/audit

将 ``HOSTNAME:PORT`` 替换为 MinIO 部署的 :abbr:`FQDN (Fully Qualified Domain Name)` 与端口。
对于使用负载均衡器管理 MinIO 节点间连接的部署，请指定负载均衡器地址。

默认情况下，MinIO 要求在抓取指标端点时进行身份验证。
如需生成所需的 bearer token，请使用 :mc:`mc admin prometheus generate`。
你也可以将 :envvar:`MINIO_PROMETHEUS_AUTH_TYPE` 设置为 ``public``，以禁用指标端点认证。

相对于基础 URL，MinIO 提供以下抓取端点：

.. list-table::
   :header-rows: 1
   :widths: 30 70
   :width: 100%

   * - 类别
     - 路径

   * - API
     - ``/api/requests``
       
       ``/bucket/api``

   * - 审计
     - ``/audit``

   * - 集群
     - ``/cluster/config``
       
       ``/cluster/erasure-set``
       
       ``/cluster/health``
       
       ``/cluster/iam``
       
       ``/cluster/usage/buckets``
       
       ``/cluster/usage/objects``

   * - 调试
     - ``/debug/go``

   * - ILM
     - ``/ilm``

   * - 日志 Webhook
     - ``/logger/webhook``

   * - 通知
     - ``/notification``

   * - 复制
     - ``/replication``
       
       ``/bucket/replication``

   * - 扫描器
     - ``/scanner``

   * - 系统
     - ``/system/drive``
       
       ``/system/memory``
       
       ``/system/cpu``
       
       ``/system/network/internode``
       
       ``/system/process``

各端点对应的完整指标列表，请参见 :ref:`Available version 3 metrics <minio-metrics-and-alerts-available-metrics>`。

   
如需在 MinIO Console 中启用历史数据可视化，请在 MinIO 部署的每个节点上设置以下环境变量：

- 将 :envvar:`MINIO_PROMETHEUS_URL` 设置为 Prometheus 服务的 URL
- 将 :envvar:`MINIO_PROMETHEUS_JOB_ID` 设置为分配给已采集指标的唯一 job ID

.. _minio-metrics-and-alerts-available-metrics:

可用的 version 3 指标
---------------------

MinIO 为集群、API 请求、存储桶以及 MinIO 服务的其他方面发布多类指标：

- :ref:`API 指标 <minio-available-v3-api-metrics>`
- :ref:`审计指标 <minio-available-v3-audit-metrics>`
- :ref:`集群指标 <minio-available-v3-cluster-metrics>`
- :ref:`调试指标 <minio-available-v3-debug-metrics>`
- :ref:`ILM 指标 <minio-available-v3-ilm-metrics>`
- :ref:`日志 Webhook 指标 <minio-available-v3-logger-webhook-metrics>`
- :ref:`通知指标 <minio-available-v3-notification-metrics>`
- :ref:`复制指标 <minio-available-v3-replication-metrics>`
- :ref:`扫描器指标 <minio-available-v3-scanner-metrics>`
- :ref:`系统指标 <minio-available-v3-system-metrics>`

许多指标都包含标签，用于标识生成该指标的资源及其他相关信息。

.. _minio-available-v3-api-metrics:

.. include:: /includes/common-metrics-v3-api.md
   :parser: myst_parser.sphinx_

.. _minio-available-v3-audit-metrics:

.. include:: /includes/common-metrics-v3-audit.md
   :parser: myst_parser.sphinx_

.. _minio-available-v3-cluster-metrics:

.. include:: /includes/common-metrics-v3-cluster.md
   :parser: myst_parser.sphinx_

.. _minio-available-v3-debug-metrics:

.. include:: /includes/common-metrics-v3-debug.md
   :parser: myst_parser.sphinx_

.. _minio-available-v3-ilm-metrics:

.. include:: /includes/common-metrics-v3-ilm.md
   :parser: myst_parser.sphinx_

.. _minio-available-v3-logger-webhook-metrics:

.. include:: /includes/common-metrics-v3-logger-webhook.md
   :parser: myst_parser.sphinx_

.. _minio-available-v3-notification-metrics:

.. include:: /includes/common-metrics-v3-notification.md
   :parser: myst_parser.sphinx_

.. _minio-available-v3-replication-metrics:

.. include:: /includes/common-metrics-v3-replication.md
   :parser: myst_parser.sphinx_

.. _minio-available-v3-scanner-metrics:

.. include:: /includes/common-metrics-v3-scanner.md
   :parser: myst_parser.sphinx_

.. _minio-available-v3-system-metrics:

.. include:: /includes/common-metrics-v3-system.md
   :parser: myst_parser.sphinx_


.. toctree::
   :titlesonly:
   :hidden:

   /operations/monitoring/collect-minio-metrics-using-prometheus
   /operations/monitoring/monitor-and-alert-using-influxdb
   /operations/monitoring/metrics-v2
