==================
``mc support top``
==================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc support top

.. note::

   .. versionchanged:: RELEASE.2022-08-11T00-30-48Z

   ``mc support top`` 替代 ``mc admin top`` 命令。

.. include:: /includes/common-mc-support.rst
   :start-after: start-minio-only
   :end-before: end-minio-only

描述
----

.. start-mc-support-top-desc

:mc:`mc support top` 命令返回分布式 MinIO 部署的统计信息，
类似于 shell 中 ``top`` 命令的输出。

.. end-mc-support-top-desc

.. note::

   :mc:`mc support top` 不支持单节点单驱动 MinIO 部署。

:mc-cmd:`mc support top` 具有以下子命令：

- :mc-cmd:`~mc support top api`
- :mc-cmd:`~mc support top locks`
- :mc-cmd:`~mc support top disk`
- :mc-cmd:`~mc support top net`
- :mc-cmd:`~mc support top rpc`

有关每个子命令的详细信息，请参阅上方链接的页面。

语法
----

该命令的语法如下：

.. code-block:: shell

   mc support top COMMAND [COMMAND FLAGS] [ARGUMENTS ...]

.. toctree::
   :titlesonly:
   :hidden:
   
   /reference/minio-mc/mc-support-top-api
   /reference/minio-mc/mc-support-top-locks
   /reference/minio-mc/mc-support-top-disk
   /reference/minio-mc/mc-support-top-net
   /reference/minio-mc/mc-support-top-rpc
