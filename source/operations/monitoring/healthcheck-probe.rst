.. _minio-healthcheck-api:

============
健康检查 API
============

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 1

MinIO 提供无需身份验证的端点，用于探测节点在线状态以及集群
:ref:`高可用性 <minio-ec-parity>`，从而执行简单健康检查。这些
端点返回一个 HTTP 状态码，用于表示底层资源是否健康，或是否满足读写仲裁。
MinIO 不会通过这些端点暴露任何其他数据。

节点存活
--------

使用以下端点测试某个 MinIO server 是否在线：

.. code-block:: shell
   :class: copyable

   curl -I https://minio.example.net:9000/minio/health/live

将 ``https://minio.example.net:9000`` 替换为待检查 MinIO server 的 DNS 主机名。

返回 ``200 OK`` 表示该 MinIO server 在线且工作正常。
任何其他 HTTP 状态码都表示访问该 server 存在问题，例如临时网络故障或潜在停机。

单靠 healthcheck probe 无法判断某个 MinIO server 是否离线。
它只能判断当前主机是否能够访问该 server。
建议配置 Prometheus :ref:`告警 <minio-metrics-and-alerts>`，使用 :ref:`metrics v3 <minio-available-v3-cluster-metrics>` 的 ``minio_cluster_health_nodes_offline_count`` 或 :ref:`metrics v2 <minio-available-cluster-metrics>` 的 ``minio_cluster_nodes_offline_total``，以检测一个或多个 MinIO 节点是否离线。

.. _minio-cluster-write-quorum:

集群写仲裁
----------

使用以下端点测试 MinIO 集群是否具备
:ref:`写仲裁 <minio-ec-parity>`：

.. code-block:: shell
   :class: copyable

   curl -I https://minio.example.net:9000/minio/health/cluster

将 ``https://minio.example.net:9000`` 替换为待检查 MinIO 集群中某个节点的 DNS 主机名。
对于使用负载均衡器管理传入连接的集群，请指定负载均衡器的主机名。

返回 ``200 OK`` 表示 MinIO 集群当前有足够的 MinIO server 在线，可满足写仲裁。
返回 ``503 Service Unavailable`` 表示集群当前不具备写仲裁。

单靠 healthcheck probe 无法判断某个 MinIO server 是否离线，也无法判断其是否正在正常处理写操作。
它只能根据配置的 :ref:`纠删码校验值 <minio-ec-parity>` 判断当前是否有足够的 MinIO server 在线，以满足写仲裁要求。
建议配置 Prometheus :ref:`告警 <minio-metrics-and-alerts>`，使用以下指标检测 MinIO 集群中的潜在问题或错误：

- ``minio_cluster_nodes_offline_total``：在一个或多个 MinIO 节点离线时触发告警。

- ``minio_node_drive_free_bytes``：在集群可用磁盘空间不足时触发告警。

集群读仲裁
----------

使用以下端点测试 MinIO 集群是否具备
:ref:`读仲裁 <minio-ec-parity>`：

.. code-block:: shell
   :class: copyable

   curl -I https://minio.example.net:9000/minio/health/cluster/read

将 ``https://minio.example.net:9000`` 替换为待检查 MinIO 集群中某个节点的 DNS 主机名。
对于使用负载均衡器管理传入连接的集群，请指定负载均衡器的主机名。

返回 ``200 OK`` 表示 MinIO 集群当前有足够的 MinIO server 在线，可满足读仲裁。
返回 ``503 Service Unavailable`` 表示集群当前不具备读仲裁。

单靠 healthcheck probe 无法判断某个 MinIO server 是否离线，也无法判断其是否正在正常处理读操作。
它只能根据配置的 :ref:`纠删码校验值 <minio-ec-parity>` 判断当前是否有足够的 MinIO server 在线，以满足读仲裁要求。
建议配置 Prometheus :ref:`告警 <minio-metrics-and-alerts>`，使用 ``minio_cluster_nodes_offline_total`` 指标检测一个或多个 MinIO 节点是否离线。

集群维护检查
------------

使用以下端点测试在将指定 MinIO server 下线维护时，
MinIO 集群是否仍能同时维持 :ref:`读 <minio-ec-parity>` 和 :ref:`写 <minio-ec-parity>`：

.. code-block:: shell
   :class: copyable

   curl -I https://minio.example.net:9000/minio/health/cluster?maintenance=true

将 ``https://minio.example.net:9000`` 替换为待检查 MinIO 集群中某个节点的 DNS 主机名。
对于使用负载均衡器管理传入连接的集群，请指定负载均衡器的主机名。

返回 ``200 OK`` 表示 MinIO 集群当前有足够的 MinIO server 在线，可满足写仲裁。
返回 ``412 Precondition Failed`` 表示如果该 MinIO server 离线，集群将失去仲裁。

单靠 healthcheck probe 无法判断某个 MinIO server 是否离线。
它只能判断在该节点因维护而下线后，是否仍有足够的 MinIO server 在线，以根据配置的 :ref:`纠删码校验值 <minio-ec-parity>` 满足读写仲裁要求。
建议配置 Prometheus :ref:`告警 <minio-metrics-and-alerts>`，使用 ``minio_cluster_nodes_offline_total`` 指标检测一个或多个 MinIO 节点是否离线。
