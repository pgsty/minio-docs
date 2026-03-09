======================
``mc support profile``
======================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 1

.. mc:: mc support profile

.. include:: /includes/common-mc-support.rst
   :start-after: start-minio-only
   :end-before: end-minio-only

描述
----

.. start-mc-support-profile-desc

:mc:`mc support profile` 为你的部署运行系统性能剖析。
剖析结果可帮助了解给定节点上运行的 MinIO 服务端进程状态。

.. end-mc-support-profile-desc

生成的报告供 MinIO Engineering 使用。
你可以将报告上传到 |subnet|。
若由你自己或第三方独立使用这些剖析结果进行诊断和修复，风险自担。

.. include:: /includes/common-mc-support.rst
   :start-after: start-minio-only
   :end-before: end-minio-only

示例
----

获取 CPU 剖析
~~~~~~~~~~~~~

此命令从别名为 ``minio1`` 的 MinIO 部署中获取 CPU 剖析数据。
剖析按默认时长运行 10 秒。

.. code-block:: shell
   :class: copyable

   mc support profile --type cpu minio1/


并发获取 CPU、内存和 block 剖析
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

此命令从别名 ``minio2`` 获取 CPU、内存和 block 使用情况的剖析数据。
剖析按默认时长运行 10 秒。

.. code-block:: shell
   :class: copyable

   mc support profile --type cpu,mem,block minio2/

并发获取 CPU、内存和 block 剖析（持续 10 分钟）
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

此命令从别名 ``minio3`` 获取 CPU、内存和 block 的剖析数据。
剖析运行 10 分钟（600 秒）。

.. code-block:: shell
   :class: copyable

   mc support profile --type cpu,mem,block --duration 600 minio3/

语法
----
      
:mc:`mc support profile` 命令语法如下：

.. code-block:: shell

   mc [GLOBALFLAGS] support profile       \
                            COMMAND       \
                            [--type]      \
                            [--airgap]    \
                            [--duration]  \
                            ALIAS 

参数
~~~~

.. mc-cmd:: --duration
   :optional:

   按指定时长运行剖析，单位为秒。

   使用 ``--type <value>``，其中 ``<value>`` 为剖析运行的秒数。

   如果未指定，命令将收集 10 秒的数据。

.. mc-cmd:: --type
   :optional:

   指定要收集数据的剖析类型。

   使用 ``--type <value>``，其中 ``<value>`` 为一个或多个以逗号分隔的数据类型。

   有效类型包括：

   - ``cpu``
   - ``cpuio``
   - ``mem``
   - ``block``
   - ``mutex``
   - ``trace``
   - ``threads``
   - ``goroutines``
   
   如果未指定，命令将收集 CPU、memory、block、mutex、threads 和 goroutines 的数据。

   .. important::

      除非 MinIO Support 明确要求，否则不要使用 ``cpuio`` 或 ``trace`` 数据类型。
      这些剖析会消耗大量资源，在缺乏指导时使用可能导致集群性能下降。

.. mc-cmd:: --airgap
   :optional:

   在无法网络访问 SUBNET 的环境中使用（例如 airgapped、firewalled 或类似配置）。

   如果部署是 airgapped，但你运行 :ref:`minio client <minio-client>` 的本地设备具有网络访问能力，则无需使用 ``--airgap`` 标志。

.. mc-cmd:: ALIAS
   :required:

   MinIO 部署的 :ref:`alias <alias>`。

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals
