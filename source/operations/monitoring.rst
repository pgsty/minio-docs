==========
监控与告警
==========

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 1

.. container:: extlinks-video

   - `使用 MinIO 和 Prometheus 进行监控：概览 <https://youtu.be/A3vCDaFWNNs?ref=docs>`__
   - `使用 MinIO 和 Prometheus 进行监控：实验 <https://youtu.be/Oix9iXndSUY?ref=docs>`__

指标与告警
----------

MinIO 使用 :prometheus-docs:`Prometheus Data Model <concepts/data_model/>` 发布时点指标。
你可以使用任何支持该数据模型的抓取工具，将这些指标拉取到数据库中，以生成历史视图、执行指标查询与分析，或基于关注的数据点创建告警。

下表列出了将 MinIO 指标接入部分第三方监控软件的教程。

.. list-table::
   :stub-columns: 1
   :widths: 30 70
   :width: 100%

   * - :ref:`minio-metrics-collect-using-prometheus`
     - 配置 Prometheus，对 MinIO 部署进行监控与告警

   * - :ref:`minio-metrics-influxdb`
     - 配置 InfluxDB，对 MinIO 部署进行监控与告警。

其他支持 Prometheus 数据模型的指标与分析软件套件，即使未出现在上表中，也可能同样可用。

日志
----

MinIO 会将所有 :mc:`minio server` 操作输出到系统控制台。
MinIO 还支持将服务日志和审计日志发布到 HTTP Webhook。

- :ref:`服务日志 <minio-logging-publish-server-logs>` 包含与系统控制台中相同的 :mc:`minio server` 操作日志。
  服务日志适用于常规监控与运维排障。

- :ref:`审计日志 <minio-logging-publish-audit-logs>` 会以更细粒度描述 MinIO 部署上的每一次操作。
  审计日志适用于需要对操作进行详细追踪的安全标准与合规要求。

MinIO 会将日志作为 JSON 文档，通过 ``PUT`` 请求发送到每个已配置的端点。
端点服务器负责处理这些 JSON 文档。
MinIO 要求显式配置每个 Webhook 端点，默认情况下 *不会* 向 Webhook 发布日志。

更完整的文档请参见 :ref:`minio-logging`。

健康检查
--------

MinIO 提供无需身份验证的端点，用于探测节点在线状态以及集群 :ref:`高可用性 <minio-ec-parity>`，从而执行简单健康检查。
这些端点只返回 HTTP 状态码。
更多信息请参见 :ref:`minio-healthcheck-api`。

.. toctree::
   :titlesonly:
   :hidden:

   /operations/monitoring/metrics-and-alerts
   /operations/monitoring/minio-logging
   /operations/monitoring/healthcheck-probe
   /operations/monitoring/grafana
