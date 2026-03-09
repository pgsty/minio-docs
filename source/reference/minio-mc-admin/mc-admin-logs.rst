=================
``mc admin logs``
=================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 1

.. mc:: mc support logs show
.. mc:: mc admin logs

.. include:: /includes/common-mc-support.rst
   :start-after: start-minio-only
   :end-before: end-minio-only

.. versionchanged:: RELEASE.2022-12-02T23-48-47Z

   ``mc support logs`` 已迁移至 ``mc admin logs``，并提供了更简化的命令接口，用于显示 MinIO 部署的服务器日志。

   对于由 systemd 管理的部署，其输出与 ``journalctl -uf minio`` 提供的内容类似。

描述
----

.. start-mc-admin-logs-desc

使用 :mc-cmd:`mc admin logs` 命令显示 MinIO 服务器日志。

.. end-mc-admin-logs-desc

.. include:: /includes/common-mc-support.rst
   :start-after: start-support-logs-opt-in
   :end-before: end-support-logs-opt-in

示例
----

显示某个部署的日志
~~~~~~~~~~~~~~~~~~

以下命令显示别名为 ``minio1`` 的部署中任意类型的最近 10 条服务器日志。

.. code-block:: shell
   :class: copyable

   mc admin logs minio1

显示某个节点最近 5 条日志条目
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下命令显示别名为 ``minio1`` 的部署中 ``node1`` 节点最近的 5 条日志条目。

.. code-block:: shell
   :class: copyable

   mc admin logs --last 5 myminio node1

显示某个部署的 application 类型日志条目
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下命令显示别名为 ``minio1`` 的部署中所有节点的 ``application`` 类型日志条目。

.. code-block:: shell
   :class: copyable

   mc admin logs --type application minio1

语法
----

命令语法如下：

.. code-block:: shell
               
   mc admin logs [GLOBAL FLAGS]     \
                 [--last, -l value] \
                 [--type, -t value] \
                 ALIAS              \
                 [NODE]

参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   MinIO 部署的 :ref:`alias <alias>`。
   
.. mc-cmd:: --last, -l
   :optional:

   仅显示最近指定数量的日志条目。

   如果不包含此标志，则最多显示最近 10 条日志条目。

.. mc-cmd:: --type, --type
   :optional:

   列出指定类型的日志条目。
   有效类型为 ``minio``、``application`` 或 ``all``。

   如果未指定，则显示所有类型的日志条目。

.. mc-cmd:: NODE
   :optional:

   在分布式部署中，输入节点名称以指定要显示其日志的节点。

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals
