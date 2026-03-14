.. _minio-metrics-influxdb:

========================================
使用 InfluxDB 进行监控与告警
========================================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 1

MinIO 使用 :prometheus-docs:`Prometheus 数据模型 <concepts/data_model/>` 发布集群和节点指标。
`InfluxDB <https://www.influxdata.com/?ref=minio>`__ 支持抓取 MinIO 指标数据，用于监控和告警。

本页介绍以下内容：

- 配置 InfluxDB 服务，抓取并展示 MinIO 部署的指标
- 基于 MinIO 指标配置告警

.. admonition:: 前提条件
   :class: note

   此过程要求满足以下条件：

   - 已有 InfluxDB 部署，并配置一个或多个 :influxdb-docs:`notification endpoints <notification-endpoints/>`
   - 已有可通过网络访问 InfluxDB 部署的 MinIO 部署
   - 本地主机已安装 :mc:`mc`，并已配置为可 :ref:`访问 <alias>` MinIO 部署

   本文说明使用 :ref:`version 2 指标 <minio-metrics-v2>`。
   关于指标 API 版本的更多信息，请参见 :ref:`指标与告警 <minio-metrics-and-alerts>`。

对于 Kubernetes 上的 MinIO 部署，本文默认已具备 Ingress、负载均衡器等必要的网络控制组件，以便 MinIO 租户与 InfluxDB 服务之间能够互相访问。

配置 InfluxDB 收集 MinIO 指标并触发告警
--------------------------------------------------

.. important::

   本过程专门使用 InfluxDB UI 来创建抓取端点。
   
   InfluxDB UI 提供的配置能力不如 `Telegraf <https://docs.influxdata.com/telegraf/v1.24/>`__ 及其对应的 `Prometheus plugin <https://github.com/influxdata/telegraf/blob/release-1.24/plugins/inputs/prometheus/README.md>`__ 完整。
   具体来说：

   - 无法通过 InfluxDB UI 为 MinIO 指标端点启用认证访问
   - 无法为已采集指标设置标签（例如 ``url_tag``），以唯一标识特定 MinIO 部署的指标

   Telegraf Prometheus plugin 还支持 Kubernetes 特有能力，例如抓取特定 MinIO 租户的 ``minio`` service。

   配置 Telegraf 不在本文范围内。
   可将本文作为配置 Telegraf 抓取 MinIO 指标的一般指导。

.. container:: procedure

   1. 配置对 MinIO 指标的公开访问

      在 MinIO 部署的所有节点上，将 :envvar:`MINIO_PROMETHEUS_AUTH_TYPE` 环境变量设置为 ``"public"``。
      随后可重启部署，以允许公开访问 MinIO 指标。

      可通过对指标端点执行 ``curl`` 来验证变更是否生效：

      .. code-block:: shell
         :class: copyable

         curl https://HOSTNAME/minio/v2/metrics/cluster

      将 ``HOSTNAME`` 替换为访问 MinIO 部署所使用的负载均衡器或反向代理 URL。
      也可以将任意单个节点写成 ``HOSTNAME:PORT``，即在节点主机名之外再指定 MinIO server API 端口。

      响应正文中应包含已采集的 MinIO 指标列表。

   #. 登录 InfluxDB UI 并创建 Bucket

      选择用于存储 MinIO 指标的 :influxdb-docs:`Organization <organizations/view-orgs/>`。

      创建一个 :influxdb-docs:`New Bucket <organizations/buckets/create-bucket/>`，用于存储该 MinIO 部署的指标。

   #. 创建新的抓取源

      创建一个 :influxdb-docs:`新的 InfluxDB Scraper <write-data/no-code/scrape-data/manage-scrapers/create-a-scraper/>`。

      指定 MinIO 部署的完整 URL，其中包含指标端点：

      .. code-block:: shell
         :class: copyable

         https://HOSTNAME/minio/v2/metrics/cluster

      将 ``HOSTNAME`` 替换为访问 MinIO 部署所使用的负载均衡器或反向代理 URL。
      也可以将任意单个节点写成 ``HOSTNAME:PORT``，即在节点主机名之外再指定 MinIO server API 端口。

   #. 验证数据

      使用 :influxdb-docs:`DataExplorer <query-data/execute-queries/data-explorer/>` 可视化已采集的 MinIO 数据。

      例如，可针对 ``minio_cluster_capacity_usable_total_bytes`` 和 ``minio_cluster_capacity_usable_free_bytes`` 设置过滤条件，以比较 MinIO 部署中的总可用空间和剩余可用空间。

   #. 配置 Check

      基于某个 MinIO 指标创建一个 :influxdb-docs:`新的 Check <https://docs.influxdata.com/influxdb/v2.4/monitor-alert/checks/create/>`。

      以下示例 Check 规则为 MinIO 部署提供了一组基础告警。
      可按需修改这些示例，或将其作为构建自定义 Check 的参考。

      - 创建一个名为 ``MINIO_NODE_DOWN`` 的 :guilabel:`Threshold Check`。
      
        将过滤条件设置为 ``minio_cluster_nodes_offline_total`` 键。
        
        在值大于 :guilabel:`1` 时，将 :guilabel:`Thresholds` 设置为 :guilabel:`WARN`。

      - 创建一个名为 ``MINIO_QUORUM_WARNING`` 的 :guilabel:`Threshold Check`。

        将过滤条件设置为 ``minio_cluster_drive_offline_total`` 键。

        当该值比已配置的 :ref:`纠删码校验值 <minio-erasure-coding>` 少 1 时，将 :guilabel:`Thresholds` 设置为 :guilabel:`CRITICAL`。

        例如，使用 ``EC:4`` 的部署应将该值设置为 ``3``。

      配置 :influxdb-docs:`Notification endpoints <monitor-alert/notification-endpoints/>` 和 :influxdb-docs:`Notification rules <monitor-alert/notification-rules/>`，使各类 Check 都能触发适当响应。
