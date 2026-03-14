.. _minio-operator-chart-values:

====================
Operator Helm 图表
====================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 1

MinIO 发布了用于 |helm-operator-chart| 和 |helm-tenant-chart| 的 |helm-charts|。
你可以通过 Helm 使用这些图表来部署 MinIO Operator 及其管理的租户。

本页说明 MinIO Operator 的 ``values.yaml`` 图表。
有关 MinIO 租户图表的文档，请参见 :ref:`minio-tenant-chart-values`

.. _minio-operator-chart-operator-values:

MinIO Operator 图表
--------------------

.. tab-set::
   
   .. tab-item:: 参考

      .. autoyaml:: /source/includes/k8s/operator-values.yaml

   .. tab-item:: YAML

      .. literalinclude:: /includes/k8s/operator-values.yaml
