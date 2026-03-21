
.. _minio-console-managing-deployment:

========================
管理部署
========================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

你可以使用 MinIO Console 执行 MinIO 提供的多种部署监控与管理功能，例如：

- 通过查看指标仪表板、服务器日志或审计日志、追踪历史、S3 事件或驱动器健康状态，:ref:`监控 <minio-console-monitoring>` 部署活动与健康状况。
- 通过添加或管理 :ref:`通知目标 <minio-console-notifications>` 来配置告警。
- 设置 :ref:`站点复制 <minio-console-site-replication>`，以同步数据中心，满足地理分散团队的及时访问需求，或用于灾难准备。
- 配置部署 :ref:`设置 <minio-console-settings>`。

.. important::

   MinIO Console 是 MinIO Server 的 Web 界面。

   它与 MinIO Kubernetes Operator Console 相互独立，二者并不相同；后者已在 Operator 6.0.0 起废弃并移除。

.. _minio-console-monitoring:

监控
----

:guilabel:`Monitoring` 部分提供用于监控 MinIO 部署的界面。

该部分包含以下子部分：
如果已认证用户不具备 :ref:`所需的管理权限 <minio-policy-mc-admin-actions>`，则某些子部分可能不可见。

指标
~~~~

Console 的 :guilabel:`Dashboard` 部分显示 MinIO 部署的指标。
默认视图提供部署状态的高层概览，包括各个服务器和驱动器的运行时长与可用性。

Console 还支持通过查询已配置为抓取 MinIO 部署数据的 :prometheus-docs:`Prometheus <prometheus/latest/getting_started/>` 服务来显示时间序列和历史数据。
具体来说，MinIO Console 使用 :prometheus-docs:`Prometheus query API <prometheus/latest/querying/api/>` 获取已存储的指标数据并显示历史指标。
有关将 MinIO 指标抓取到 Prometheus 的更多信息，请参见 :ref:`minio-metrics-collect-using-prometheus`。

日志
~~~~

Console 的 :guilabel:`Logs` 部分显示 MinIO 部署生成的 :ref:`服务器日志 <minio-logging>`。

- 使用 :guilabel:`Nodes` 下拉框将日志过滤到 MinIO 部署中的部分服务器节点。

- 使用 :guilabel:`Log Types` 下拉框将日志过滤到部分日志类型。

- 使用 :guilabel:`Filter` 对日志结果应用文本过滤条件

选择 :guilabel:`Start Logs` 按钮，开始使用所选过滤器和设置采集日志。

审计
~~~~

.. important::

   MinIO 计划废弃 Tenant Console Audit Log 功能，并在后续版本中将其移除。
   作为替代方案，可使用任何支持 Webhook 的数据库或日志服务，从 Tenant 捕获 :ref:`审计日志 <minio-logging-publish-audit-logs>`。

Audit Log 部分提供用于查看由已配置 PostgreSQL 服务采集的 :ref:`审计日志 <minio-logging>` 的界面。

追踪
~~~~

:guilabel:`Trace` 部分为部署中的一个或多个存储桶提供 HTTP 追踪功能。
该部分提供与 :mc:`mc admin trace` 类似的功能。

你可以修改追踪范围，仅显示特定的追踪调用。
默认仅显示与 :guilabel:`S3` 相关的 HTTP 追踪。
      
选择 :guilabel:`Filters` 可打开附加过滤器并应用到追踪输出，例如将追踪适用的 :guilabel:`Path` 限制为某个特定存储桶或存储桶前缀。

观察
~~~~

:guilabel:`Watch` 部分显示所选存储桶上发生的 S3 事件。
该部分提供与 :mc:`mc watch` 类似的功能。

加密
~~~~

:guilabel:`Encryption` 部分允许你查看已配置 :kes-docs:`Key Encryption Service <>` 提供方的状态和指标。

.. _minio-console-notifications:

事件
----

.. versionchanged:: Console 0.23.1

   Notifications 部分重命名为 Events。

:guilabel:`Events` 部分提供用于查看、添加或删除 :ref:`事件通知 <minio-bucket-notifications>` 目标的界面。

你可以使用此界面配置 MinIO，将通知事件推送到一个或多个目标端，包括 Redis、MySQL、Kafka、PostgreSQL、AMQP、MQTT、Elastic Search、NATS、NSQ 或 Webhook。

选择 :guilabel:`Add Event Destination +` 按钮，为部署添加新的事件目标。

你可以从列表中选择现有通知目标，查看其详细信息或删除该目标。

.. _minio-console-site-replication:

站点复制
--------

:guilabel:`Site Replication` 部分提供用于添加和管理部署 :ref:`站点复制 <minio-site-replication-overview>` 配置的界面。

配置站点复制时，要求现有存储桶或对象（如果有）只能存在于单个站点中。

.. _minio-console-encryption:

加密
----

:guilabel:`Encryption` 设置提供用于列出、创建和删除密钥的界面，这些密钥可用于 :ref:`MinIO 服务端加密 <minio-sse>`。

你可以将此视图中创建或列出的密钥用于对象加密操作，包括设置 :ref:`存储桶级默认密钥 <minio-console-buckets>`。

.. important::

   删除密钥会导致 MinIO 无法解密任何受该密钥保护的对象。
   如果该密钥不存在备份，删除密钥将使对象永久不可读。
   更多信息请参见 :ref:`minio-encryption-sse-secure-erasure-locking`。

.. _minio-console-settings:

配置
----

:guilabel:`Settings` 部分提供用于查看和获取部署中所有 MinIO Server 的 :ref:`配置设置 <minio-server-configuration-settings>` 的界面。
使用 :guilabel:`Export` 和 :guilabel:`Import` 按钮可在不同部署之间导出和导入设置。

该部分包含以下子部分。

- Region
- Compression
- API
- Heal
- Scanner
- Etcd
- Logger Webhook
- Audit :ref:`Webhook <minio-bucket-notifications-publish-webhook>`
- Audit :ref:`Kafka <minio-bucket-notifications-publish-kafka>`

.. versionadded:: Console v0.24.0

   环境变量中的配置设置会覆盖在 MinIO Console 中添加的任何自定义内容。
   将鼠标悬停在配置字段上方，可显示工具提示，说明该设置是否由环境变量控制。

如果已认证用户不具备 :ref:`所需的管理权限 <minio-policy-mc-admin-actions>`，则某些子部分可能不可见。

该界面的功能与使用 :mc-cmd:`mc admin config get` 或 :mc-cmd:`mc admin config set` 类似。
有关如何定义这些选项的详细信息，请参见这些命令。

某些配置设置可能需要重启 MinIO 部署才能生效。
