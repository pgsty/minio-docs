.. _minio-tenant-chart-values:

==================
租户 Helm Charts
==================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 1

MinIO 为 |helm-operator-chart| 和 |helm-tenant-chart| 发布了 |helm-charts|。
你可以使用这些 Chart 通过 Helm 部署 MinIO Operator 和受管租户。

以下页面说明 MinIO 租户的 ``values.yaml`` Chart。
有关 MinIO Operator Chart 的文档，请参阅 :ref:`minio-operator-chart-values`

.. _minio-tenant-chart-operator-values:


MinIO 租户 Chart
------------------

.. tab-set::

   .. tab-item:: 参考

      .. autoyaml:: /source/includes/k8s/tenant-values.yaml

   .. tab-item:: YAML

      .. literalinclude:: /includes/k8s/tenant-values.yaml
