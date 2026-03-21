.. _minio-metrics-collect-using-prometheus:

========================================
使用 Prometheus 进行监控与告警
========================================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 1

.. container:: extlinks-video

   - `使用 MinIO 和 Prometheus 进行监控：概览 <https://youtu.be/A3vCDaFWNNs?ref=docs>`__
   - `使用 MinIO 和 Prometheus 进行监控：实验 <https://youtu.be/Oix9iXndSUY?ref=docs>`__

MinIO 使用 :prometheus-docs:`Prometheus 数据模型 <concepts/data_model/#data-model>` 发布集群、节点、存储桶和资源指标。
本页过程说明了以下内容：

- 配置 Prometheus 服务，抓取并展示 MinIO 部署的指标
- 基于某个 MinIO 指标配置 Alert Rule，以触发 AlertManager 动作

本文说明使用 :ref:`version 2 指标 <minio-metrics-v2>`。
关于指标 API 版本的更多信息，请参见 :ref:`指标与告警 <minio-metrics-and-alerts>`。

.. admonition:: 前提条件
   :class: note

   此过程要求满足以下条件：

   - 已有 :prometheus-docs:`Prometheus 部署 <prometheus/latest/installation/>`，并配置 :prometheus-docs:`Alert Manager <alerting/latest/overview/>`

   - 已有可通过网络访问 Prometheus 部署的 MinIO 部署

   - 本地主机已安装 :mc:`mc`，并已配置为可 :ref:`访问 <alias>` MinIO 部署


配置 Prometheus 收集 MinIO 指标并触发告警
------------------------------------------------------------

1) 生成抓取配置
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc:`mc admin prometheus generate` 命令生成 Prometheus 执行抓取请求所需的 scrape 配置：

.. tab-set::

   .. tab-item:: MinIO Server

      以下命令用于抓取 MinIO 集群的指标。

      .. code-block:: shell
         :class: copyable
      
         mc admin prometheus generate ALIAS

      将 :mc-cmd:`ALIAS <mc admin prometheus generate ALIAS>` 替换为该 MinIO 部署的 :mc:`alias <mc alias>`。
	 
      该命令将返回类似如下的输出：

      .. code-block:: yaml
         :class: copyable
      
         global:
            scrape_interval: 60s
         
         scrape_configs:
            - job_name: minio-job
              bearer_token: TOKEN
              metrics_path: /minio/v2/metrics/cluster
              scheme: https
              static_configs:
              - targets: [minio.example.net]
		      
   .. tab-item:: 节点

      以下命令用于抓取 MinIO Server 上某个节点的指标。

      .. code-block:: shell
         :class: copyable
      
         mc admin prometheus generate ALIAS node

      将 :mc-cmd:`ALIAS <mc admin prometheus generate ALIAS>` 替换为该 MinIO 部署的 :mc:`alias <mc alias>`。

      .. code-block:: yaml
         :class: copyable
      
         global:
            scrape_interval: 60s
         
         scrape_configs:
            - job_name: minio-job-node
              bearer_token: TOKEN
              metrics_path: /minio/v2/metrics/node
              scheme: https
              static_configs:
              - targets: [minio-1.example.net, minio-2.example.net, minio-N.example.net]
		      
   .. tab-item:: 存储桶

      以下命令用于抓取 MinIO Server 上存储桶的指标。

      .. code-block:: shell
         :class: copyable
      
         mc admin prometheus generate ALIAS bucket

      将 :mc-cmd:`ALIAS <mc admin prometheus generate ALIAS>` 替换为该 MinIO 部署的 :mc:`alias <mc alias>`。

      .. code-block:: yaml
         :class: copyable
      
         global:
            scrape_interval: 60s
         
         scrape_configs:
            - job_name: minio-job-bucket
              bearer_token: TOKEN
              metrics_path: /minio/v2/metrics/bucket
              scheme: https
              static_configs:
              - targets: [minio.example.net]
      
   .. tab-item:: 资源

      .. versionadded:: RELEASE.2023-10-07T15-07-38Z

      以下命令用于抓取 MinIO Server 上资源的指标。

      .. code-block:: shell
         :class: copyable

         mc admin prometheus generate ALIAS resource

      将 :mc-cmd:`ALIAS <mc admin prometheus generate ALIAS>` 替换为该 MinIO 部署的 :mc:`alias <mc alias>`。

      .. code-block:: yaml
         :class: copyable

         global:
            scrape_interval: 60s

         scrape_configs:
            - job_name: minio-job-resource
              bearer_token: TOKEN
              metrics_path: /minio/v2/metrics/resource
              scheme: https
              static_configs:
              - targets: [minio.example.net]
      
- 设置合适的 ``scrape_interval``，确保每次抓取操作都能在下一次开始前完成。
  推荐值为 60 秒。

  某些部署由于需要抓取的指标数量较多，可能需要更长的抓取间隔。
  为降低 MinIO 和 Prometheus Server 的负载，请选择满足监控要求的最长间隔。

- 将 ``job_name`` 设置为与该 MinIO 部署相关的值。

  请使用唯一值，以确保该部署的指标与同一 Prometheus 服务采集的其他指标彼此隔离。

- 对于以 :envvar:`MINIO_PROMETHEUS_AUTH_TYPE` 设置为 ``"public"`` 启动的 MinIO 部署，可以省略 ``bearer_token`` 字段。

- 对于未使用 TLS 的 MinIO 部署，请将 ``scheme`` 设置为 ``http``。

- 在 ``targets`` 数组中设置能够解析到 MinIO 部署的主机名。

  这可以是任意单个节点，也可以是负责处理与 MinIO 节点连接的负载均衡器或代理。

  对于 Kubernetes 基础设施上的 MinIO Tenant，如果使用的是同一集群中的 Prometheus 集群，则可以指定 ``minio`` service 的 DNS 名称。
  否则，你可以指定已配置为向 MinIO Tenant 路由连接的 ingress 或 load balancer 端点。

2) 使用更新后的配置重启 Prometheus
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

将上一步生成的目标 ``scrape_configs`` job 追加到配置文件中：

.. tab-set::

   .. tab-item:: 集群

      集群指标会聚合节点级指标，并在适用时为指标附加来源节点的标签。

      .. code-block:: yaml
         :class: copyable
      
         global:
            scrape_interval: 60s
         
         scrape_configs:
            - job_name: minio-job
              bearer_token: TOKEN
              metrics_path: /minio/v2/metrics/cluster
              scheme: https
              static_configs:
              - targets: [minio.example.net]


   .. tab-item:: 节点

      节点指标专用于节点级监控。该配置需要列出所有 MinIO 节点。

      .. code-block:: yaml
         :class: copyable
      
         global:
            scrape_interval: 60s
         
         scrape_configs:
            - job_name: minio-job-node
              bearer_token: TOKEN
              metrics_path: /minio/v2/metrics/node
              scheme: https
              static_configs:
              - targets: [minio-1.example.net, minio-2.example.net, minio-N.example.net]

	      
   .. tab-item:: 存储桶

      .. code-block:: yaml
         :class: copyable
      
         global:
            scrape_interval: 60s
         
         scrape_configs:
            - job_name: minio-job-bucket
              bearer_token: TOKEN
              metrics_path: /minio/v2/metrics/bucket
              scheme: https
              static_configs:
              - targets: [minio.example.net]

   .. tab-item:: 资源

      .. code-block:: yaml
         :class: copyable

         global:
            scrape_interval: 60s

         scrape_configs:
            - job_name: minio-job-resource
              bearer_token: TOKEN
              metrics_path: /minio/v2/metrics/resource
              scheme: https
              static_configs:
              - targets: [minio.example.net]

使用该配置文件启动 Prometheus 集群：

.. code-block:: shell
   :class: copyable

   prometheus --config.file=prometheus.yaml

3) 分析已采集指标
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Prometheus 内置 :prometheus-docs:`expression browser <prometheus/latest/getting_started/#using-the-expression-browser>`。
你可以在其中执行查询，以分析已采集的指标。

.. tab-set::

   .. tab-item:: 示例

      以下查询示例会返回抓取任务名为 ``minio-job`` 的 Prometheus 每五分钟采集一次的指标：

      .. code-block:: shell
         :class: copyable

         minio_node_drive_free_bytes{job="minio-job"}[5m]
         minio_node_drive_free_inodes{job="minio-job"}[5m]

         minio_node_drive_latency_us{job="minio-job"}[5m]

         minio_node_drive_offline_total{job="minio-job"}[5m]
         minio_node_drive_online_total{job="minio-job"}[5m]

         minio_node_drive_total{job="minio-job"}[5m]

         minio_node_drive_total_bytes{job="minio-job"}[5m]
         minio_node_drive_used_bytes{job="minio-job"}[5m]

         minio_node_drive_errors_timeout{job="minio-job"}[5m]
         minio_node_drive_errors_availability{job="minio-job"}[5m]

         minio_node_drive_io_waiting{job="minio-job"}[5m]

   .. tab-item:: 推荐指标

      MinIO 建议将以下指标作为基础监控项。

      所有可用指标的信息请参见 :ref:`minio-metrics-and-alerts`。

      .. list-table::
         :header-rows: 1
         :widths: 40 60
         :width: 100%

         * - 指标
           - 说明
	     
         * - ``minio_node_drive_free_bytes``
           - 驱动器上的总可用存储空间。

         * - ``minio_node_drive_free_inodes``
           - 总空闲 inode 数量。

         * - ``minio_node_drive_latency_us``
           - 最近一分钟内驱动器 API 存储操作的平均延迟，单位为 µs。

         * - ``minio_node_drive_offline_total``
           - 该节点中离线驱动器的总数。

         * - ``minio_node_drive_online_total``
           - 该节点中在线驱动器的总数。

         * - ``minio_node_drive_total``
           - 该节点中的驱动器总数。

         * - ``minio_node_drive_total_bytes``
           - 驱动器上的总存储空间。

         * - ``minio_node_drive_used_bytes``
           - 驱动器上已使用的存储空间总量。

         * - ``minio_node_drive_errors_timeout``
           - 自 server 启动以来驱动器超时错误的总数。

         * - ``minio_node_drive_errors_availability``
           - 自 server 启动以来驱动器 I/O 错误、权限拒绝和超时的总数。

         * - ``minio_node_drive_io_waiting``
           - 驱动器上等待中的 I/O 操作总数。

4) 使用 MinIO 指标配置告警规则
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

你必须在 Prometheus 部署上配置 :prometheus-docs:`Alert Rules <prometheus/latest/configuration/alerting_rules/>`，以根据已采集的 MinIO 指标触发告警。

以下示例告警规则文件为 MinIO 部署提供了一组基础告警。
你可以修改这些示例，或将其作为构建自定义告警的参考。

.. code-block:: yaml
   :class: copyable

   groups:
   - name: minio-alerts
     rules:
     - alert: NodesOffline
       expr: avg_over_time(minio_cluster_nodes_offline_total{job="minio-job"}[5m]) > 0
       for: 10m
       labels:
         severity: warn
       annotations:
         summary: "Node down in MinIO deployment"
         description: "Node(s) in cluster {{ $labels.instance }} offline for more than 5 minutes"

     - alert: DisksOffline
       expr: avg_over_time(minio_cluster_drive_offline_total{job="minio-job"}[5m]) > 0
       for: 10m
       labels:
         severity: warn
       annotations:
         summary: "Disks down in MinIO deployment"
         description: "Disks(s) in cluster {{ $labels.instance }} offline for more than 5 minutes"

在 Prometheus 配置中，请在 ``rule_files`` 键中指定告警文件路径：

.. code-block:: yaml

   rule_files:
   - minio-alerting.yml

一旦触发，Prometheus 会将告警发送到已配置的 AlertManager 服务。

仪表板
------

MinIO 提供 Grafana 仪表板，用于展示由 Prometheus 采集的指标。
更多信息请参见 :ref:`minio-grafana`
