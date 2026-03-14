================================
``mc admin prometheus generate``
================================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc admin prometheus generate

描述
-----------

.. start-mc-admin-prometheus-generate-desc

:mc:`mc admin prometheus generate` 命令会生成一个用于 `Prometheus <https://prometheus.io/>`__ 的指标抓取配置文件。

.. end-mc-admin-prometheus-generate-desc

有关 MinIO 与 Prometheus 配合使用的完整文档，请参见 :ref:`How to monitor MinIO server with Prometheus <minio-metrics-collect-using-prometheus>`

从 MinIO 服务端 :minio-release:`RELEASE.2024-07-15T19-02-30Z` 和 MinIO 客户端 :mc-release:`RELEASE.2024-07-11T18-01-28Z` 开始，:ref:`metrics version 3 (v3) <minio-metrics-and-alerts>` 提供了额外的端点和指标。
要生成 v3 抓取配置，请使用 ``--api_version v3`` 选项。

MinIO 建议新部署使用 :ref:`version 3 (v3) <minio-metrics-and-alerts>`。
现有部署可以继续使用 :ref:`metrics version 2 <minio-metrics-v2>`

.. admonition:: 仅在 MinIO 部署上使用 ``mc admin``
   :class: note

   .. include:: /includes/facts-mc-admin.rst
      :start-after: start-minio-only
      :end-before: end-minio-only

.. tab-set::

   .. tab-item:: 示例

      以下命令会为 :term:`alias` ``myminio`` 指向的部署生成一个 Prometheus 抓取配置，用于采集 version 2 的存储桶指标：

      .. code-block:: shell
         :class: copyable

         mc admin prometheus generate myminio bucket

   .. tab-item:: 语法

      该命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] admin prometheus generate                                        \
                                           ALIAS                                           \
                                           [TYPE]                                          \
                                           [--api_version v3]                              \
                                           [--bucket <bucket name>]

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


参数
~~~~~~~~~~

.. mc-cmd:: ALIAS
   :required:

   已配置 MinIO 部署的 :mc:`alias <mc alias>`，该命令会为该部署生成与 Prometheus 兼容的配置文件。

.. mc-cmd:: --api-version
   :optional:

   要为 :ref:`v3 metrics <minio-metrics-and-alerts>` 生成抓取配置，请添加 ``--api-version v3`` 参数。
   ``v3`` 是唯一接受的值。

   省略 ``--api-version`` 可生成 :ref:`v2 metrics <minio-metrics-v2>` 配置。

.. mc-cmd:: --bucket
   :optional:

   仅适用于 v3 指标。

   对于返回存储桶级指标的 v3 指标类型，请指定存储桶名称。
   需要 :mc-cmd:`~mc admin prometheus generate --api-version`。

   ``--bucket`` 适用于以下 v3 指标类型：

   - ``api``
   - ``replication``

   以下示例为存储桶 ``mybucket`` 的 API 指标生成配置：

   .. code-block:: shell
      :class: copyable

      mc admin prometheus generate ALIAS api --bucket mybucket --api-version v3

.. mc-cmd:: TYPE
   :optional:

   要抓取的指标类型。

      metrics version 3 的有效值为：

      - ``api``
      - ``audit``
      - ``cluster``
      - ``debug``
      - ``ilm``
      - ``logger``
      - ``notification``
      - ``replication``
      - ``scanner``
      - ``system``

      如果未指定，``v3`` 命令会返回所有指标。

      metrics version 2 的有效值为：

      - ``bucket``
      - ``cluster``
      - ``node``
      - ``resource``

      如果未指定，``v2`` 命令返回 cluster 指标。
      Cluster 指标包含部分 node 指标的汇总。


全局参数
~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals


示例
--------

生成默认的 v3 metrics 配置
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc admin prometheus generate --api-version v3 <mc admin prometheus generate --api-version>` 生成一个抓取配置，用于为 MinIO 部署采集所有 v3 指标：

.. code-block:: shell
   :class: copyable

   mc admin prometheus generate ALIAS --api-version v3

- 将 ``ALIAS`` 替换为 MinIO 部署的 :mc-cmd:`alias <mc alias>`。

输出类似如下：

.. code-block:: shell

   scrape_configs:
   - job_name: minio-job
     bearer_token: [auth token]
     metrics_path: /minio/metrics/v3
     scheme: http
     static_configs:
     - targets: ['localhost:9000']


为其他类型生成 v3 metrics 配置
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

要为其他指标类型生成配置，请指定类型。
以下命令会为 v3 cluster 类型指标生成抓取配置：

.. code-block:: shell
   :class: copyable

   mc admin prometheus generate ALIAS cluster --api-version v3

- 将 ``ALIAS`` 替换为 MinIO 部署的 :mc-cmd:`alias <mc alias>`。

输出类似如下：

.. code-block:: shell

   scrape_configs:
   - job_name: minio-job-cluster
     bearer_token: [auth token]
     metrics_path: /minio/metrics/v3/cluster
     scheme: http
     static_configs:
     - targets: ['localhost:9000']

要为 :mc-cmd:`different metric type <mc admin prometheus generate TYPE>` 生成配置，请将 ``cluster`` 替换为所需类型。


生成 v3 bucket replication metrics 配置
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下示例会为存储桶 ``mybucket`` 的 v3 replication 指标生成抓取配置：

.. code-block:: shell
   :class: copyable

   mc admin prometheus generate ALIAS replication --bucket mybucket --api-version v3

- 将 ``ALIAS`` 替换为 MinIO 部署的 :mc-cmd:`alias <mc alias>`。

输出类似如下：

.. code-block:: shell

   scrape_configs:
   - job_name: minio-job-replication
     bearer_token: [auth token]
     metrics_path: /minio/metrics/v3/bucket/replication/mybucket
     scheme: https
     static_configs:
     - targets: [`localhost:9000`]

生成用于存储桶 API 指标的 v3 配置
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下示例会为存储桶 ``mybucket`` 的 v3 API 指标生成抓取配置：

.. code-block:: shell
   :class: copyable

   mc admin prometheus generate ALIAS api --bucket mybucket --api-version v3

- 将 ``ALIAS`` 替换为 MinIO 部署的 :mc-cmd:`alias <mc alias>`。

输出类似如下：

.. code-block:: shell

   scrape_configs:
   - job_name: minio-job-api
     bearer_token: [auth token]
     metrics_path: /minio/metrics/v3/bucket/api/mybucket
     scheme: https
     static_configs:
     - targets: [`localhost:9000`]


生成默认的 v2 metrics 配置
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

默认情况下，:mc-cmd:`mc admin prometheus generate` 会生成用于 v2 cluster 指标的抓取配置：

.. code-block:: shell
   :class: copyable

   mc admin prometheus generate ALIAS

- 将 ``ALIAS`` 替换为 MinIO 部署的 :mc-cmd:`alias <mc alias>`。

输出类似如下：

.. code-block:: shell

   scrape_configs:
   - job_name: minio-job
     bearer_token: [auth token]
     metrics_path: /minio/v2/metrics
     scheme: http
     static_configs:
     - targets: ['localhost:9000']


为其他指标类型生成 v2 配置
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

要为其他指标类型生成配置，请指定类型。
以下命令会为 v2 bucket 类型指标生成抓取配置：

.. code-block:: shell
   :class: copyable

   mc admin prometheus generate ALIAS bucket
