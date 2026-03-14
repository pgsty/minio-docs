===========================
``mc admin scanner status``
===========================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc admin scanner info
.. mc:: mc admin scanner status

描述
-----------

.. start-mc-admin-scanner-status-desc

:mc-cmd:`mc admin scanner status` 命令会显示 MinIO Server 的 :ref:`scanner <minio-concepts-scanner>` 信息的实时摘要。

.. end-mc-admin-scanner-status-desc

该命令的别名为 ``mc admin scanner info``。

.. admonition:: 仅在 MinIO 部署上使用 ``mc admin``
   :class: note

   .. include:: /includes/facts-mc-admin.rst
      :start-after: start-minio-only
      :end-before: end-minio-only

.. tab-set::

   .. tab-item:: 示例

      以下示例返回扫描器进程当前状态的信息。

      .. code-block:: shell
         :class: copyable

         mc admin scanner status myminio

      该命令返回的结果类似如下：

      .. code-block:: shell
         
         Overall Statistics                               	
         ------------------                                                  	
         Last full scan time:   0d0h15m; Estimated 2879.79/month             	
         Current cycle:         (between cycles)                             	
         Active drives: 0                                                    	
                                                                             	
         Last Minute Statistics                                              	
         ----------------------                                              	
         Objects Scanned:       3 objects; Avg: 67.611µs; Rate: 4320/day     	
         Versions Scanned:      3 versions; Avg: 2.506µs; Rate: 4320/day     	
         Versions Heal Checked: 0 versions; Avg: 0ms                         	
         Read Metadata:         3 objects; Avg: 40.817µs, Size: 395 bytes/obj	
         ILM checks:            3 versions; Avg: 714ns                       	
         Check Replication:     3 versions; Avg: 892ns                       	
         Verify Deleted:        0 folders; Avg: 0ms                          	
         Yield:                 18ms total; Avg: 6ms/obj     

   .. tab-item:: 语法

      该命令的语法如下：

      .. code-block:: shell
         :class: copyable

         mc admin scanner status ALIAS
                                [--bucket <string>]     \
                                [--interval <value>]   \
                                [--max-paths <value>]  \
                                [-n <integer>]         \
                                [--nodes <string>] 

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~~~~~~~

.. mc-cmd:: ALIAS
   :required:

   用于显示 :ref:`scanner <minio-concepts-scanner>` API 操作的 MinIO 部署 :ref:`alias <alias>`。

.. mc-cmd:: --bucket
   :optional:

   将 scanner 统计信息过滤到指定存储桶。

.. mc-cmd:: --interval
   :optional:

   两次状态请求刷新之间等待的秒数。
   如果未指定，状态每 3 秒刷新一次。

.. mc-cmd:: --max-paths
   :optional:

   要显示的活跃路径的最大数量。
   使用 ``-1`` 表示不限制路径数量。

   当被扫描的驱动器数量较大时，限制显示路径数量可以减少控制台窗口滚动。
   
   如果未指定，结果将返回不限制数量的活跃路径。

.. mc-cmd:: -n
   :optional:

   在自动退出前返回的状态请求数量。
   使用 ``0`` 表示返回不限数量的状态结果。

   如果未指定，结果会按指定间隔持续刷新，直到手动退出。

.. mc-cmd:: --nodes
   :optional:

   返回指定节点的 scanner 状态信息。
   使用逗号分隔列表指定多个节点。


全局标志
~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals
