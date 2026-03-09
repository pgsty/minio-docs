.. _minio-metrics-v2:

=================
Metrics version 2
=================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 3


MinIO 使用 :prometheus-docs:`Prometheus Data Model <concepts/data_model/>` 发布集群和节点指标。
你可以使用任意抓取工具从 MinIO 拉取指标数据，以执行进一步分析和配置告警。

Version 2 端点
--------------

Metrics version 2 将指标划分为以下三个类别：

- :ref:`集群指标 <minio-available-cluster-metrics>`
- :ref:`存储桶指标 <minio-available-bucket-metrics>`
- :ref:`资源指标 <minio-available-resource-metrics>`

每个 v2 端点都会返回其所属类别的全部指标。
例如，抓取以下端点会返回所有集群指标：

.. code-block:: shell
   :class: copyable

   http://HOSTNAME:PORT/minio/v2/metrics/cluster

仅访问基础端点 ``/minio/v2/metrics/`` 也会返回集群指标。

如需更灵活的抓取方式和更广泛的指标集合，请使用 :ref:`metrics version 3 <minio-metrics-and-alerts>`。
现有部署仍可继续使用 version 2 :ref:`指标 <minio-metrics-v2>` 和 :ref:`Grafana 仪表板 <minio-grafana>`。


MinIO Grafana 仪表板
--------------------

MinIO 提供两个 :ref:`Grafana 仪表板 <minio-grafana>`，用于可视化 v2 指标。
有关为 Grafana 配置兼容 Prometheus 数据源的完整说明，请参见 :prometheus-docs:`Prometheus 关于 Grafana 支持的文档 <visualization/grafana/>`。


可用的 version 2 指标
---------------------

以下各节描述 version 2 的端点与指标。

.. tab-set::

   .. tab-item:: 集群指标

      你可以使用以下 URL 端点抓取 :ref:`集群级指标 <minio-available-cluster-metrics>`：

      .. code-block:: shell
         :class: copyable

         http://HOSTNAME:PORT/minio/v2/metrics/cluster

      将 ``HOSTNAME:PORT`` 替换为 MinIO 部署的 :abbr:`FQDN (Fully Qualified Domain Name)` 与端口。
      对于使用负载均衡器管理 MinIO 节点间连接的部署，请指定负载均衡器地址。


   .. tab-item:: 存储桶指标

      .. versionchanged:: MinIO RELEASE.2023-07-21T21-12-44Z

         存储桶指标已迁移到独立端点。

      .. versionchanged:: RELEASE.2023-08-31T15-31-16Z

         你可以使用以下 URL 端点抓取 :ref:`存储桶级指标 <minio-available-bucket-metrics>`：

      .. versionchanged:: RELEASE.2025-03-12T17-29-24Z

         出于性能原因，v2 指标最多支持 100 个存储桶。
         如果需要覆盖更多存储桶的指标，请改用 :ref:`v3 指标 <minio-metrics-and-alerts-available-metrics>`。
     
      .. code-block:: shell
         :class: copyable

         http://HOSTNAME:PORT/minio/v2/metrics/bucket

      将 ``HOSTNAME:PORT`` 替换为 MinIO 部署的 :abbr:`FQDN (Fully Qualified Domain Name)` 与端口。
      对于使用负载均衡器管理 MinIO 节点间连接的部署，请指定负载均衡器地址。


   .. tab-item:: 资源指标

      .. versionadded:: RELEASE.2023-10-07T15-07-38Z 

      你可以使用以下 URL 端点抓取 :ref:`资源指标 <minio-available-resource-metrics>`：

      .. code-block:: shell
         :class: copyable

         http://HOSTNAME:PORT/minio/v2/metrics/resource

      将 ``HOSTNAME:PORT`` 替换为 MinIO 部署的 :abbr:`FQDN (Fully Qualified Domain Name)` 与端口。
      对于使用负载均衡器管理 MinIO 节点间连接的部署，请指定负载均衡器地址。


- :ref:`集群指标 <minio-available-cluster-metrics>`
- :ref:`存储桶指标 <minio-available-bucket-metrics>`
- :ref:`资源指标 <minio-available-resource-metrics>`

.. _minio-available-cluster-metrics:

.. include:: /includes/common-metrics-cluster.md
   :parser: myst_parser.sphinx_

.. _minio-available-bucket-metrics:

   .. versionchanged:: RELEASE.2025-03-12T17-29-24Z

      出于性能原因，v2 指标最多支持 100 个存储桶。
      如果需要覆盖更多存储桶的指标，请改用 :ref:`v3 指标 <minio-metrics-and-alerts-available-metrics>`。


.. include:: /includes/common-metrics-bucket.md
   :parser: myst_parser.sphinx_

.. _minio-available-resource-metrics:

.. include:: /includes/common-metrics-resource.md
   :parser: myst_parser.sphinx_
