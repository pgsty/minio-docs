.. start-minio-only

.. admonition:: 需要完成 SUBNET 注册
   :class: note

   ``mc support`` 命令面向已在 |subnet| 注册的 MinIO 部署设计，以确保诊断和
   性能测试获得最佳结果。
   未注册 SUBNET 的部署无法使用 ``mc support`` 命令。

.. end-minio-only

.. start-support-logs-opt-in

上传功能默认保持禁用，只有在某个部署上明确选择加入后才会启用。
如果已经启用，你可以随时使用 :mc-cmd:`mc support callhome disable`
关闭该功能。

.. end-support-logs-opt-in
