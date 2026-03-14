====================
``mc admin profile``
====================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc admin profile

.. note:: 

   自 `mc` RELEASE.2023-04-06T16-51-10Z 起，该命令已由 :mc:`mc support profile` 取代。

描述
----

.. start-mc-admin-profile-desc

:mc-cmd:`mc admin profile` 命令会生成用于调试的性能分析数据。

.. end-mc-admin-profile-desc

.. admonition:: 仅在 MinIO 部署上使用 ``mc admin``
   :class: note

   .. include:: /includes/facts-mc-admin.rst
      :start-after: start-minio-only
      :end-before: end-minio-only

性能分析数据格式
~~~~~~~~~~~~~~~~

:mc-cmd:`mc admin profile` 会生成一个 ``ZIP`` 归档文件 ``profile.zip``，
其中包含一个或多个 ``.pprof`` 文件。使用
`pprof <https://github.com/google/pprof>`__ ``go`` 工具读取这些性能分析数据。

示例
----

单个资源的性能分析数据
~~~~~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc admin profile start` 并结合
:mc-cmd:`~mc admin profile start type` 标志开始对该资源进行性能分析：

.. code-block:: shell
   :class: copyable

   mc admin profile start --type "TYPE" ALIAS

- 将 :mc-cmd:`ALIAS <mc admin profile start TARGET>` 替换为 MinIO 主机的
  :mc-cmd:`alias <mc alias>`。

- 将 :mc-cmd:`TYPE <mc admin profile start type>` 替换为要分析的资源类型。

使用 :mc-cmd:`mc admin profile stop` 停止对指定资源的性能分析并输出结果：

.. code-block:: shell
   :class: copyable

   mc admin profile stop

该命令会将性能分析数据输出为 ``profile.zip``。

多个资源的性能分析数据
~~~~~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc admin profile start` 并结合
:mc-cmd:`~mc admin profile start type` 标志开始对这些资源进行性能分析：

.. code-block:: shell
   :class: copyable

   mc admin profile start --type "TYPE,[TYPE...]" ALIAS

- 将 :mc-cmd:`ALIAS <mc admin profile start TARGET>` 替换为 MinIO 主机的
  :mc-cmd:`alias <mc alias>`。

- 将 :mc-cmd:`TYPE <mc admin profile start type>` 替换为要分析的资源类型。
  多个资源类型请使用逗号分隔列表指定。

使用 :mc-cmd:`mc admin profile stop` 停止对指定资源的性能分析并输出结果：

.. code-block:: shell
   :class: copyable

   mc admin profile stop

该命令会将性能分析数据输出为 ``profile.zip``。

语法
----

:mc-cmd:`mc admin profile` 的语法如下：

.. code-block:: shell
   :class: copyable

   mc admin profile SUBCOMMAND

:mc-cmd:`mc admin profile` 支持以下子命令：

.. mc-cmd:: start
   :fullpath:

   在目标 MinIO 部署上开始收集性能分析数据。该命令语法如下：

   .. code-block:: shell
      :class: copyable

      mc admin profile start [FLAGS] TARGET

   :mc-cmd:`mc admin profile start` 支持以下参数：

   .. mc-cmd:: TARGET

      已配置 MinIO 部署的 :mc-cmd:`alias <mc alias>`，命令将从该部署收集性能分析数据。

   .. mc-cmd:: type
      

      从 :mc-cmd:`~mc admin profile start TARGET` MinIO 部署收集的性能分析数据类型。

      将以下一个或多个支持的类型以逗号分隔列表形式指定：

      - ``cpu``
      - ``mem``
      - ``block``
      - ``mutex``
      - ``trace``
      - ``threads``
      - ``goroutines``

      省略时默认为 ``cpu,mem,block``。

.. mc-cmd:: stop
   :fullpath:

   停止性能分析过程，并将收集的数据作为 ``profile.zip`` 返回。该 ``zip``
   文件包含一个或多个 ``.pprof`` 文件，可由 ``go``
   `pprof <https://github.com/google/pprof>`__ 等工具读取。

   该命令语法如下：

   .. code-block:: shell
      :class: copyable

      mc admin profile stop TARGET

   该命令支持以下参数：

   .. mc-cmd:: TARGET

      已配置 MinIO 部署的 :mc-cmd:`alias <mc alias>`，命令将从该部署返回可用的性能分析数据。

