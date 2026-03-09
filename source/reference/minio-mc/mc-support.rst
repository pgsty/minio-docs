==============
``mc support``
==============

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc support


描述
----

.. start-mc-support-desc

MinIO Client :mc:`mc support` 命令提供用于分析部署健康状况或性能、并运行诊断的工具。
你还可以上传生成的健康报告，供 MinIO 工程团队进一步分析。

.. end-mc-support-desc

.. important::

   ``mc support`` 命令需要有效的 |SUBNET| 注册。
   
   :mc-cmd:`mc support proxy set` 和 :mc-cmd:`mc support proxy remove` 是例外，因为你可能需要先配置代理才能完成部署注册。


子命令
------

:mc:`mc support` 包含以下子命令：

.. list-table::
   :header-rows: 1
   :widths: 30 70
   :width: 100%

   * - 子命令
     - 描述

   * - :mc:`~mc support callhome`
     - .. include:: /reference/minio-mc/mc-support-callhome.rst
          :start-after: start-mc-support-callhome-desc
          :end-before: end-mc-support-callhome-desc

   * - :mc:`~mc support diag`
     - .. include:: /reference/minio-mc/mc-support-diag.rst
          :start-after: start-mc-support-diag-desc
          :end-before: end-mc-support-diag-desc

   * - :mc:`~mc support inspect`
     - .. include:: /reference/minio-mc/mc-support-inspect.rst
          :start-after: start-mc-support-inspect-desc
          :end-before: end-mc-support-inspect-desc

   * - :mc:`~mc support perf`
     - .. include:: /reference/minio-mc/mc-support-perf.rst
          :start-after: start-mc-support-perf-desc
          :end-before: end-mc-support-perf-desc

   * - :mc:`~mc support profile`
     - .. include:: /reference/minio-mc/mc-support-profile.rst
          :start-after: start-mc-support-profile-desc
          :end-before: end-mc-support-profile-desc

   * - :mc:`~mc support proxy`
     - .. include:: /reference/minio-mc/mc-support-proxy.rst
          :start-after: start-mc-support-proxy-desc
          :end-before: end-mc-support-proxy-desc

   * - :mc:`~mc support top`
     - .. include:: /reference/minio-mc/mc-support-top.rst
          :start-after: start-mc-support-top-desc
          :end-before: end-mc-support-top-desc

.. toctree::
   :titlesonly:
   :hidden:
   
   /reference/minio-mc/mc-support-callhome
   /reference/minio-mc/mc-support-diag
   /reference/minio-mc/mc-support-inspect
   /reference/minio-mc/mc-support-perf
   /reference/minio-mc/mc-support-profile
   /reference/minio-mc/mc-support-proxy
   /reference/minio-mc/mc-support-top
   /reference/minio-mc/mc-support-upload
