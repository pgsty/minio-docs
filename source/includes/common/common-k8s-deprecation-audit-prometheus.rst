.. start-deprecate-audit-logs

.. important::

   MinIO 计划弃用租户 Console Audit Log 功能，
   并在后续版本中移除。
   MinIO 建议提前禁用该功能，为此变更做好准备。

   作为替代方案，可使用任意支持 webhook 的数据库或日志服务，
   来接收租户的 :ref:`审计日志 <minio-logging-publish-audit-logs>`。

.. end-deprecate-audit-logs

.. start-deprecate-prometheus

.. important::

   MinIO 计划弃用租户 Prometheus pod 功能，
   并在后续版本中移除。
   MinIO 建议提前将该值设置为 ``false``，为此变更做好准备。

   作为替代方案，可使用部署在 Kubernetes 集群内或集群外的任意 Prometheus 服务，
   来 :ref:`采集租户指标 <minio-metrics-collect-using-prometheus>`。

.. end-deprecate-prometheus
