.. _minio-concepts-scanner:

==============
对象扫描器
==============

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 1

概览
--------

MinIO 使用内置扫描器检查对象是否需要自愈，并执行任何已计划的对象操作。
这类操作可能包括：

- 计算驱动器上的数据使用量
- 评估并应用已配置的 :ref:`生命周期管理 <minio-lifecycle-management>` 或 :ref:`对象保留 <minio-object-retention>` 规则
- 执行 :ref:`存储桶 <minio-bucket-replication>` 或 :ref:`站点 <minio-site-replication-overview>` 复制
- 检查对象是否存在丢失或损坏的数据分片或校验分片，并执行 :ref:`自愈 <minio-concepts-healing>`

扫描器在两个层级执行这些功能：集群层级和存储桶层级。
在集群层级，扫描器会将所有存储桶划分为多个组，并一次扫描其中一组。
扫描器会先处理自上次扫描以来新增的存储桶，然后再以随机顺序扫描其他存储桶。
在完成所有存储桶组的检查后，扫描器会重新开始新一轮扫描。

在存储桶层级，扫描器会对存储桶内条目分组，并从该存储桶中选择部分条目进行扫描。
扫描器会根据对象名称的哈希值来选择要扫描的对象。
在 16 轮扫描的周期内，MinIO 会检查命名空间中的每个对象。
对于自上次扫描以来确认新增的任何前缀，MinIO 会执行完整扫描。

扫描时长
-----------

影响一次扫描完成时间的因素有很多。

其中包括：

- 提供给 MinIO 的驱动器类型
- 可用吞吐量和 :abbr:`iops (input/output operations per second)`
- 对象数量和大小
- MinIO Server 上的其他活动

例如，默认情况下，MinIO 会暂停扫描器，以便将 I/O 资源让给读写请求。
这会拉长扫描完成所需的时间。

MinIO 在每次扫描之间的等待时间，会按本次扫描操作耗时乘以一个系数来计算。
默认情况下，该系数为 ``10.0``，表示 MinIO 会在一次扫描完成后等待相当于该操作耗时 10 倍的时间，再开始下一次扫描。
这个系数会根据配置的 :ref:`扫描器速度设置 <minio-scanner-speed-options>` 发生变化。

扫描器性能
-------------------

影响扫描器性能的因素有很多。
其中包括：

- 可用节点资源
- 集群规模
- 纠删码集合数量相对于驱动器数量的关系
- 存储桶层级结构的复杂度（对象和前缀）

例如，在相同硬件和工作负载条件下，一个从 100TB 数据增长到 200TB 数据的集群，扫描完整个存储桶和对象命名空间所需的时间会更长。
同样地，单个包含 16 块驱动器的纠删码集合，其扫描耗时会长于把相同数量驱动器拆分为两个 8 驱动器纠删码集合的情况。

MinIO 将扫描器视为后台任务，并会暂停它以优先完成集群中的读写请求。
随着集群规模或工作负载增长，为保证普通 S3 操作的优先级，扫描器会更频繁地让出资源，因此其性能会下降。

.. include:: /includes/common/scanner.rst
   :start-after: start-scanner-speed-config
   :end-before: end-scanner-speed-config

扫描器指标
---------------

MinIO 提供了若干 `与扫描器相关的指标 <https://silo.pigsty.cc/operations/monitoring/metrics-v2.html#scanner-metrics>`__。

使用 ``mc admin scanner info`` 可以查看扫描器当前状态，以及距离上次完整扫描所经过的时间。
这有助于理解扫描器操作提供的各项指标。

扫描器指标（包括 usage 指标）反映的是最近一次完成的扫描结果。
自上次扫描以来发生的 ``PUT`` 或 ``DELETE`` 操作，不会在 usage 中体现，直到受影响的存储桶完成下一次扫描。


输出类似如下：

.. code-block::

   Overall Statistics                                                                              
   ------------------                                                                              
   Last full scan time:   0d0h14m; Estimated 2885.28/month                                         
   Current cycle:         70464; Started: 2024-04-19 20:02:34.568479139 +0000 UTC                  
   Active drives:         2                                                                        
                                                                                                   
   Last Minute Statistics                                                                          
   ----------------------                                                                          
   Objects Scanned:       620 objects; Avg: 124.929µs; Rate: 892800/day                            
   Versions Scanned:      620 versions; Avg: 2.801µs; Rate: 892800/day                             
   Versions Heal Checked: 0 versions; Avg: 0ms                                                     
   Read Metadata:         621 objects; Avg: 88.416µs, Size:
   ILM checks:            656 versions; Avg: 663ns                        	
   Check Replication:     656 versions; Avg: 1.061µs                      	
   Verify Deleted:        0 folders; Avg: 0ms                             	
   Yield:                 3.086s total; Avg: 4.705ms/obj   
