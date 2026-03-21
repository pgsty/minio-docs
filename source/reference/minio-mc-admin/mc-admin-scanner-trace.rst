==========================
``mc admin scanner trace``
==========================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc admin scanner trace

说明
----

.. start-mc-admin-scanner-trace-desc

:mc-cmd:`mc admin scanner trace` 命令会显示目标 MinIO 部署上发生的 :ref:`scanner <minio-concepts-scanner>` 相关 API 操作。

.. end-mc-admin-scanner-trace-desc

.. admonition:: 仅在 MinIO 部署上使用 ``mc admin``
   :class: note

   .. include:: /includes/facts-mc-admin.rst
      :start-after: start-minio-only
      :end-before: end-minio-only

.. tab-set::

   .. tab-item:: 示例

      以下示例返回 ``myminio`` 部署上与 scanner 相关的 API 操作列表。

      .. code-block:: shell
         :class: copyable

         mc admin scanner trace myminio

   .. tab-item:: 语法

      命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc admin scanner trace ALIAS
                                [--filter-request]            \
                                [--filter-response]           \
                                [--filter-size <value>]       \
                                [--funcname <value>]          \
                                [--node <value>]              \
                                [--path <value>]              \
                                [--response-duration <value>] \
                                [--verbose, -v]

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   要显示其 :ref:`scanner <minio-concepts-scanner>` API 操作的 MinIO 部署的 :ref:`alias <alias>`。

.. mc-cmd:: --filter-request
   :optional:

   跟踪请求大小大于指定 :mc-cmd:`~mc admin scanner trace --filter-size` 值的 scanner 操作或调用。

   **必须** 与 :mc-cmd:`~mc admin scanner trace --filter-size` 标志配合使用。

.. mc-cmd:: --filter-response
   :optional:

   跟踪响应大小大于指定 :mc-cmd:`~mc admin scanner trace --filter-size` 值的 scanner 操作或调用。

   **必须** 与 :mc-cmd:`~mc admin scanner trace --filter-size` 标志配合使用。

.. mc-cmd:: --filter-size
   :optional:

   将输出过滤为请求大小或响应大小大于指定大小的条目。

   必须与 :mc-cmd:`~mc admin scanner trace --filter-request` 或 :mc-cmd:`~mc admin scanner trace --filter-response` 标志之一配合使用。

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
        - TB（Terabyte，1000 Gigabytes）
   
      * - ``ki``
        - KiB（Kibibyte，1024 Bytes）
   
      * - ``mi``
        - MiB（Mebibyte，1024 Kibibytes）
   
      * - ``gi``
        - GiB（Gibibyte，1024 Mebibytes）
   
      * - ``ti``
        - TiB（Tebibyte，1024 Gibibytes）

.. mc-cmd:: --funcname
   :optional:

   返回与输入函数名对应的调用。

.. mc-cmd:: --node
   :optional:

   返回指定服务器的调用。

.. mc-cmd:: --path
   :optional:

   返回指定路径的调用。

.. mc-cmd:: --response-duration
   :optional:

   跟踪响应持续时间大于指定值的调用。

.. mc-cmd:: --verbose, -v
   :optional:

   返回详细输出。


全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals


示例
----

监控所有 scanner API 操作
~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc admin scanner trace` 监控别名为 ``myminio`` 的 MinIO 部署上的 :ref:`scanner <minio-concepts-scanner>` API 操作：

.. code-block:: shell
   :class: copyable

   mc admin scanner trace myminio

显示特定路径的 scanner 跟踪
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc admin scanner trace` 监控别名为 ``myminio`` 的部署上路径 ``my-bucket/my-prefix/*`` 的 API 操作：

.. code-block:: shell
   :class: copyable
   
   mc admin scanner trace --path my-bucket/my-prefix/* myminio

显示 ``scanObject`` 函数的 scanner API 操作
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

监控 ``myminio`` 部署上 ``scanObject`` 函数的 scanner 活动：

.. code-block:: shell
   :class: copyable

   mc admin scanner trace --funcname=scanner.ScanObject myminio 

显示大于 ``1MB`` 的 scanner 操作请求
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc admin scanner trace` 监控 ``myminio`` 部署上大于 ``1MB`` 的请求：

.. code-block:: shell
   :class: copyable

   mc admin scanner trace --filter-request --filter-size 1MB myminio

显示大于 ``1MB`` 的 scanner 操作响应
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc admin scanner trace` 监控较大的响应大小：

.. code-block:: shell
   :class: copyable

   mc admin scanner trace --filter-response --filter-size 1MB myminio 

显示持续超过五毫秒的 scanner 操作
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc admin scanner trace` 监控耗时较长的操作：

.. code-block:: shell
   :class: copyable

   mc admin scanner trace --response-duration 5ms myminio
