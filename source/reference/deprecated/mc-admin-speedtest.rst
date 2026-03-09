======================
``mc admin speedtest``
======================

.. default-domain:: minio

.. contents:: Table of Contents
   :local:
   :depth: 2

.. mc:: mc admin speedtest

.. versionchanged:: RELEASE.2022-07-24T02-25-13Z

   ``mc admin speedtest`` 已由 :mc:`mc support perf` 替代。

描述
-----------

.. start-mc-admin-speedtest-desc

:mc-cmd:`mc admin speedtest` 命令通过 ``PUT`` 和 ``GET`` 操作测试每个主机的吞吐量。

.. end-mc-admin-speedtest-desc

:mc-cmd:`~mc admin speedtest` 从 ``mc``
:mc-release:`RELEASE.2021-09-02T09-21-27Z` 开始可用，并支持运行 :minio-release:`RELEASE.2021-07-30T00-02-00Z` 或更高版本的分布式 MinIO 部署。

:mc-cmd:`~mc admin speedtest` 不支持独立部署或 MinIO Gateway 部署。

.. admonition:: 仅在 MinIO 部署上使用 ``mc admin``
   :class: note

   .. include:: /includes/facts-mc-admin.rst
      :start-after: start-minio-only
      :end-before: end-minio-only

语法
------

:mc-cmd:`mc admin speedtest` 使用以下语法：

.. code-block:: shell
   :class: copyable

   mc admin speedtest [FLAGS] TARGET

:mc-cmd:`mc admin speedtest` 支持以下参数：

.. mc-cmd:: TARGET

   *必需*

   已配置 MinIO 部署的 :mc-cmd:`alias <mc alias>`，用于执行 speedtest。

.. mc-cmd:: --duration
   

   整个 speedtest 的运行时长。默认为 ``10s``。

.. mc-cmd:: --size
   

   用于上传/下载的对象大小。默认为 ``64MiB``。

.. mc-cmd:: --concurrent
   

   每个服务器的并发请求数。默认为 ``32``。
