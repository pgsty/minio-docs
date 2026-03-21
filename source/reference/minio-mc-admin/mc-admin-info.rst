=================
``mc admin info``
=================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 1

.. mc:: mc admin info

描述
----

.. start-mc-admin-info-desc

:mc-cmd:`mc admin info` 命令显示 MinIO 服务器的信息。
对于分布式 MinIO 部署，:mc-cmd:`mc admin info` 会显示部署中每个 MinIO
服务器的信息。

.. end-mc-admin-info-desc

.. versionadded:: mc RELEASE.2024-05-03T11-21-07Z

   命令输出包含集群的 :ref:`erasure code <minio-ec-erasure-set>` 设置信息。
   该信息在输出中以 ``EC:#`` 格式显示。

命令输出如下所示：

.. code-block::

   ●  play.min.io
      Uptime: 2 hours 
      Version: 2024-05-10T08:24:14Z
      Network: 1/1 OK 
      Drives: 4/4 OK 
      Pool: 1
   
   Pools:
      1st, Erasure sets: 1, Drives per erasure set: 4
   
   0 B Used, 3 Buckets, 0 Objects
   4 drives online, 0 drives offline, EC:1
   

示例
----

.. include:: /includes/play-alias-available.rst
   :start-after: play-alias-only
   :end-before: end-play-alias-only

.. code-block:: shell
   :class: copyable

   mc admin info play

语法
----

:mc-cmd:`mc admin info` 语法如下：

.. code-block:: shell
   :class: copyable

   mc admin info TARGET      \
                 [--offline]

将已配置 MinIO 部署的 :mc-cmd:`alias <mc alias>` 指定为 ``TARGET``。

参数
~~~~

.. mc-cmd:: TARGET
   :required:
   
   要显示信息的部署 :ref:`alias <alias>`。

.. mc-cmd:: --offline
   :optional:

   仅显示离线驱动器或节点。
