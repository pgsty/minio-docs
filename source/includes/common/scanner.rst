.. start-scanner-speed-config

你可以使用 :envvar:`MINIO_SCANNER_SPEED` 环境变量或
:mc-conf:`scanner speed <scanner.speed>` 配置项，
调整 MinIO 在扫描器性能与读写操作之间的平衡方式。

.. end-scanner-speed-config


.. start-scanner-speed-values

MinIO 使用 :ref:`scanner <minio-concepts-scanner>` 执行
:ref:`存储桶复制 <minio-bucket-replication>`、
:ref:`站点复制 <minio-site-replication-overview>`、
:ref:`生命周期管理 <minio-lifecycle-management>` 和
:ref:`自愈 <minio-concepts-healing>` 任务。

有效值包括：

.. list-table::
   :stub-columns: 1
   :widths: 30 70
   :width: 100%
   
   * - ``fastest``
     - 移除扫描器在读/写延迟上的等待时间，使扫描器以最高速度和 IOPS 消耗运行。
       此设置可能导致读取和写入性能下降。
   
   * - ``fast``
     - 将扫描器在读/写延迟上的等待时间设置为较短，
       使扫描器以更高速度和 IOPS 消耗运行。
       此设置可能导致读取和写入性能下降。
   
   * - ``default``
     - 将扫描器在读/写延迟上的等待时间设置为中等，
       使扫描器以平衡的速度和 IOPS 消耗运行。
       此设置旨在在保持读写性能的同时允许扫描器持续工作。
   
   * - ``slow``
     - 将扫描器在读/写延迟上的等待时间设置为中等，
       此时扫描器以较低速度和 IOPS 消耗运行。
       该设置在降低扫描器性能的同时，可提供更好的读写性能。

       可能影响依赖扫描器的功能，例如生命周期管理和复制。

   * - ``slowest``
     - 将扫描器在读/写延迟上的等待时间设置为较长，
       此时扫描器以显著更低的速度和 IOPS 消耗运行。
       该设置优先保障读写操作，但可能以牺牲扫描器操作为代价。

       可能影响依赖扫描器的功能，例如生命周期管理和复制。

.. end-scanner-speed-values
