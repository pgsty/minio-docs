===============================
``mc admin prometheus metrics``
===============================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc admin prometheus metrics

说明
----

.. start-mc-admin-prometheus-metrics-desc

:mc:`mc admin prometheus metrics` 命令用于输出集群的 Prometheus 指标。

.. end-mc-admin-prometheus-metrics-desc

输出还包含每个指标的附加信息，例如其值类型是 ``counter`` 还是 ``gauge``。

有关将 MinIO 与 Prometheus 配合使用的完整文档，请参阅 :ref:`How to monitor MinIO server with Prometheus <minio-metrics-collect-using-prometheus>`

从 MinIO 服务端 :minio-release:`RELEASE.2024-07-15T19-02-30Z` 和 MinIO 客户端 :mc-release:`RELEASE.2024-07-11T18-01-28Z` 开始，:ref:`metrics version 3 (v3) <minio-metrics-and-alerts>` 提供了额外的端点和指标。
要输出 v3 指标，请使用 ``--api_version v3`` 选项。

MinIO 建议新部署使用 :ref:`version 3 (v3) <minio-metrics-and-alerts>`。
现有部署可以继续使用 :ref:`metrics version 2 <minio-metrics-v2>`。

.. admonition:: 仅在 MinIO 部署上使用 ``mc admin``
   :class: note

   .. include:: /includes/facts-mc-admin.rst
      :start-after: start-minio-only
      :end-before: end-minio-only

.. tab-set::

   .. tab-item:: 示例

      以下命令输出 :term:`alias` ``myminio`` 对应部署的集群指标：

      .. code-block:: shell
         :class: copyable

         mc admin prometheus metrics myminio cluster

   .. tab-item:: 语法

      命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] admin prometheus metrics  \
                                           ALIAS                                           \
                                           [TYPE]                                          \
                                           [--api_version v3]                              \
                                           [--bucket <bucket name>]


      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   已配置 MinIO 部署的 :mc:`alias <mc alias>`，命令将输出该部署的指标。

.. mc-cmd:: --api-version
   :optional:

   要输出 :ref:`version 3 (v3) <minio-metrics-and-alerts>` 指标，请添加 ``--api-version v3`` 参数。
   ``v3`` 是唯一接受的值。

   省略 ``--api-version`` 则输出 :ref:`version 2 (v2) <minio-metrics-v2>` 指标。

.. mc-cmd:: --bucket
   :optional:

   需要 :mc-cmd:`~mc admin prometheus metrics --api-version`。
   对于返回存储桶级指标的 v3 指标类型，需指定存储桶名称。

   ``--bucket`` 适用于以下 v3 指标类型：

   - ``api``
   - ``replication``

   以下示例输出存储桶 ``mybucket`` 的 API 指标：

   .. code-block:: shell
      :class: copyable

      mc admin prometheus metrics ALIAS api --bucket mybucket --api-version v3

.. mc-cmd:: TYPE
   :optional:

   要输出的指标类型。

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

      未指定时，``v3`` 命令返回所有指标。

      metrics version 2 的有效值为：

      - ``bucket``
      - ``cluster``
      - ``node``
      - ``resource``

      未指定时，``v2`` 命令返回集群指标。
      集群指标包含部分节点指标的汇总值。


全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals


示例
----

输出 v3 指标
~~~~~~~~~~~~

使用 :mc-cmd:`mc admin prometheus metrics --api-version v3 <mc admin prometheus metrics --api-version>` 输出某个 MinIO 部署中所有可用的 v3 指标及其当前值：

.. code-block:: shell
   :class: copyable

   mc admin prometheus metrics ALIAS --api-version v3

- 将 ``ALIAS`` 替换为 MinIO 部署的 :mc-cmd:`alias <mc alias>`。

要输出特定类型的指标，请添加 :mc-cmd:`~mc admin prometheus metrics TYPE`。
以下命令输出某个部署的全部 scanner 指标：

.. code-block:: shell
   :class: copyable

   mc admin prometheus metrics ALIAS scanner --api-version v3

输出 v3 API 或存储桶复制指标
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

某些 v3 指标类型接受 :mc-cmd:`~mc admin prometheus metrics --bucket` 参数，用于指定要输出指标的存储桶。
以下示例输出存储桶 ``mybucket`` 的 v3 replication 指标：

.. code-block:: shell
   :class: copyable

   mc admin prometheus metrics ALIAS replication --bucket mybucket --api-version v3

- 将 ``ALIAS`` 替换为 MinIO 部署的 :mc-cmd:`alias <mc alias>`。

要输出该存储桶的 API 指标，请将 ``replication`` 替换为 ``api``。


输出 v2 集群指标
~~~~~~~~~~~~~~~~

默认情况下，:mc-cmd:`mc admin prometheus metrics` 输出 v2 集群指标：

.. code-block:: shell
   :class: copyable

   mc admin prometheus metrics ALIAS

- 将 ``ALIAS`` 替换为 MinIO 部署的 :mc-cmd:`alias <mc alias>`。


输出其他类型的 v2 指标
~~~~~~~~~~~~~~~~~~~~~~

要输出另一种 v2 指标类型，请指定所需的 :mc-cmd:`~mc admin prometheus metrics TYPE`。
以下示例输出 v2 bucket 指标：

.. code-block:: shell
   :class: copyable

   mc admin prometheus metrics ALIAS bucket

可接受的值为 ``bucket``、``cluster``、``node`` 和 ``resource``。
