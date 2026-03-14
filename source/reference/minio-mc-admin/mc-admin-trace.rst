==================
``mc admin trace``
==================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc admin trace

说明
----

.. start-mc-admin-trace-desc

:mc-cmd:`mc admin trace` 命令显示目标 MinIO 部署上发生的 API 操作。

.. end-mc-admin-trace-desc

.. admonition:: 仅在 MinIO 部署上使用 ``mc admin``
   :class: note

   .. include:: /includes/facts-mc-admin.rst
      :start-after: start-minio-only
      :end-before: end-minio-only

示例
----

监控所有 API 操作
~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc admin trace` 监控 MinIO 部署上的 API 操作：

.. code-block:: shell
   :class: copyable

   mc admin trace -a ALIAS

- 将 :mc-cmd:`ALIAS <mc admin trace TARGET>` 替换为 MinIO 部署的 :mc-cmd:`alias <mc alias>`。

查看返回 503 错误的调用
~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc admin trace` 监控返回 503 Service Unavailable 错误的 API 操作：

.. code-block:: shell
   :class: copyable
   
   mc admin trace -v --status-code 503 ALIAS

- 将 :mc-cmd:`ALIAS <mc admin trace TARGET>` 替换为 MinIO 部署的 :mc-cmd:`alias <mc alias>`。

查看指定路径的控制台跟踪
~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc admin trace` 监控指定路径的活动：

.. code-block:: shell
   :class: copyable

   mc admin trace --path my-bucket/my-prefix/* ALIAS

- 将 :mc-cmd:`ALIAS <mc admin trace TARGET>` 替换为 MinIO 部署的 :mc-cmd:`alias <mc alias>`。
- 将 ``my-bucket/my-prefix/*`` 替换为你要跟踪的存储桶、前缀和对象名称或通配符。

查看响应大小大于 1Mb 的控制台跟踪
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc admin trace` 监控超过指定大小的响应：

.. code-block:: shell
   :class: copyable

   mc admin trace --filter-response --filter-size 1Mb ALIAS

- 将 :mc-cmd:`ALIAS <mc admin trace TARGET>` 替换为 MinIO 部署的 :mc-cmd:`alias <mc alias>`。
- 将 ``1Mb`` 替换为所需的响应大小。

查看请求操作时长大于 5ms 的控制台跟踪
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc admin trace` 监控耗时较长的操作：

.. code-block:: shell
   :class: copyable

   mc admin trace --filter-duration --filter-size 5ms ALIAS

- 将 :mc-cmd:`ALIAS <mc admin trace TARGET>` 替换为 MinIO 部署的 :mc-cmd:`alias <mc alias>`。

语法
----

:mc-cmd:`mc admin trace` 的语法如下：

.. code-block:: shell
   :class: copyable

   mc admin trace [FLAGS] TARGET

:mc-cmd:`mc admin trace` 支持以下参数：

.. mc-cmd:: TARGET

   指定已配置 MinIO 部署的 :mc:`alias <mc alias>`，用于监控其 API 操作。

.. mc-cmd:: --all, a
   
   返回 MinIO 部署上的所有流量，包括 MinIO 服务器之间的节点间流量。

.. mc-cmd:: --call

   仅跟踪匹配的客户端操作或调用类型。
   例如，以下命令仅跟踪 ``scanner`` 类型的操作。

   .. code-block:: shell

      mc admin trace --call scanner TARGET

   有效的调用类型包括：

   - ``batch-keyrotation``
   - ``batch-replication``
   - ``bootstrap``
   - ``decommission``
   - ``ftp``
   - ``healing``
   - ``ilm``
   - ``internal``
   - ``os``
   - ``rebalance``
   - ``replication-resync``
   - ``s3``
   - ``scanner``
   - ``storage``

   如果未指定，MinIO 返回 ``s3`` 类型的调用。

.. mc-cmd:: --errors, e
   
   仅返回失败的 API 操作。

.. mc-cmd:: --filter-request

   跟踪请求大小大于指定 :mc-cmd:`~mc admin trace --filter-size` 值的客户端操作或调用。

   必须与 :mc-cmd:`~mc admin trace --filter-size` 标志一起使用。

.. mc-cmd:: --filter-response

   跟踪响应大小大于指定 :mc-cmd:`~mc admin trace --filter-size` 值的客户端操作或调用。

   必须与 :mc-cmd:`~mc admin trace --filter-size` 标志一起使用。

.. mc-cmd:: --filter-size

   过滤后的客户端操作或调用的大小限制。

   必须与 :mc-cmd:`~mc admin trace --filter-request` 或 :mc-cmd:`~mc admin trace --filter-response` 标志之一一起使用。

   有效单位包括：

   .. list-table::
      :header-rows: 1
      :widths: 20 80
      :width: 100%
   
      * - 后缀
        - 单位大小
   
      * - ``k``
        - KB（Kilobyte，1000 Bytes）
   
      * - ``m``
        - MB（Megabyte，1000 Kilobytes）
   
      * - ``g``
        - GB（Gigabyte，1000 Megabytes）
   
      * - ``t``
        - TB（Terrabyte，1000 Gigabytes）
   
      * - ``ki``
        - KiB（Kibibyte，1024 Bites）
   
      * - ``mi``
        - MiB（Mebibyte，1024 Kibibytes）
   
      * - ``gi``
        - GiB（Gibibyte，1024 Mebibytes）
   
      * - ``ti``
        - TiB（Tebibyte，1024 Gibibytes）

.. mc-cmd:: --funcname

   返回输入函数名对应的调用。

.. mc-cmd:: --method

   返回指定 HTTP 方法的调用。

.. mc-cmd:: --node

   返回指定服务器的调用。

.. mc-cmd:: --path

   返回指定路径的调用。

.. mc-cmd:: --request-header

   返回与提供的请求头匹配的调用。

.. mc-cmd:: --request-query

   返回与提供的请求查询参数匹配的调用。
   此调试选项只能在 MinIO Support 指导下使用。

.. mc-cmd:: --response-duration

   跟踪响应时长大于指定值的调用。

.. mc-cmd:: --response-threshold

   接受时间字符串作为值，例如 ``5ms``。
   仅返回响应时间大于所提供阈值的调用。

   如果未指定，MinIO 返回响应时间大于 5ms 的调用。

.. mc-cmd:: --status-code

   返回指定 HTTP 状态码的调用。

.. mc-cmd:: --stats

   在当前跟踪会话期间，为每个被跟踪的函数调用累积聚合统计信息。

   输出表包含以下列。

   .. list-table::
      :stub-columns: 1
      :widths: 30 70

      * - Call
        - 捕获到的客户端操作或函数名称。

      * - Count
        - 客户端操作或调用发生的次数。

      * - RPM
        - 客户端操作或调用的每分钟速率（Rate Per Minute，RPM）。

      * - Avg Time
        - 客户端操作或调用完成所需的平均时间。

      * - Min Time
        - 客户端操作或调用完成所用的最短时间。

      * - Max Time
        - 客户端操作或调用完成所用的最长时间。

      * - Avg TTFB
        - .. versionadded:: RELEASE.2023-11-15T22-45-58Z

          客户端操作或调用响应的平均首字节时间（Time To First Byte，TTFB）。

      * - Max TTFB
        - .. versionadded:: RELEASE.2023-11-15T22-45-58Z
        
          客户端操作或调用响应的最大首字节时间。

      * - Avg Size
        - 客户端操作或调用响应的平均大小。

      * - Errors
        - 因错误失败的客户端操作或调用数量。

      * - RX Avg
        - 客户端操作或调用的平均接收字节数（Bytes Received，RX）。
          此统计仅在值不为零（0）时显示。 

      * - TX AVG
        - 客户端操作或调用的平均发送字节数（Bytes Sent，TX）。
          此统计仅在值不为零（0）时显示。 

   累积统计信息，例如名称、计数、持续时间、最短时间、最长时间、首字节时间或错误。
   最多累积 15 条统计条目。

.. mc-cmd:: --verbose
   
   返回详细输出。


全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals
