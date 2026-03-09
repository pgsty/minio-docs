========================
``mc support top locks``
========================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc support top locks

.. include:: /includes/common-mc-support.rst
   :start-after: start-minio-only
   :end-before: end-minio-only

语法
----

.. start-mc-support-top-locks-desc

:mc:`mc support top locks` 命令列出 MinIO 部署中最旧的 10 个 :ref:`锁 <minio-object-locking>`。

.. end-mc-support-top-locks-desc

该命令输出锁的存在时长、锁类型、所有者和资源。
输出类似如下：

.. code-block:: shell

   Since                 Type    Owner                 Resource
   13 hours ago          WRITE   10.68.100.18:9000     .minio.sys/leader.lock
   13 hours ago          WRITE   10.68.100.18:9000     .minio.sys/callhome/runCallhome.lock
   13 hours ago          WRITE   10.68.100.23:9000     .minio.sys/new-drive-healing/0/0

.. tab-set::

   .. tab-item:: 示例

      以下命令显示 :term:`alias` ``myminio`` 上当前进行中的 S3 API 调用。

      .. code-block:: shell
         :class: copyable

         mc support top locks myminio/

   .. tab-item:: 语法

      该命令使用以下语法：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] support top locks  \
                          [--stale]          \ 
                          TARGET

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~

.. mc-cmd:: TARGET
   :required:

   命令应在其上运行的 :ref:`alias <minio-mc-alias>` 或前缀的完整路径。

.. mc-cmd:: --stale
   :optional:

   仅返回陈旧锁。

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

显示 ``myminio`` 部署中最旧的 10 个锁
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: shell
   :class: copyable

   mc support top locks myminio/

显示 ``myminio`` 部署中的陈旧锁
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下命令显示 ``myminio`` 部署中所有进行中的 ``s3.PutObject`` 调用：

.. code-block:: shell
   :class: copyable

   mc support top locks --stale myminio/
