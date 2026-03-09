.. _minio-mc-ilm-ls:

=============
``mc ilm ls``
=============

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc ilm ls

.. versionchanged:: RELEASE.2022-12-24T15-21-38Z

   ``mc ilm ls`` 已由 :mc-cmd:`mc ilm rule ls` 替代。


语法
----

.. start-mc-ilm-ls-desc

:mc:`mc ilm ls` 命令以表格形式汇总 MinIO 存储桶上已配置的所有对象生命周期管理规则。

.. end-mc-ilm-ls-desc

该命令的输出可能如下所示：

.. code-block:: shell

   ┌───────────────────────────────────────────────────────────────────────────────┐
   │ Transition for latest version (Transition)                                    │
   ├────────┬─────────┬────────┬─────────────────────┬──────────────┬──────────────┤
   │ ID     │ STATUS  │ PREFIX │ TAGS                │ DAYS TO TIER │ TIER         │
   ├────────┼─────────┼────────┼─────────────────────┼──────────────┼──────────────┤
   │ rule-1 │ Enabled │ doc/   │ key1=val1&key2=val2 │            0 │ WARM-MINIO-1 │
   └────────┴─────────┴────────┴─────────────────────┴──────────────┴──────────────┘
   ┌────────────────────────────────────────────────────────────────┐
   │ Transition for older versions (NoncurrentVersionTransition)    │
   ├────────┬─────────┬────────┬──────┬──────────────┬──────────────┤
   │ ID     │ STATUS  │ PREFIX │ TAGS │ DAYS TO TIER │ TIER         │
   ├────────┼─────────┼────────┼──────┼──────────────┼──────────────┤
   │ rule-2 │ Enabled │ logs/  │ -    │           10 │ WARM-MINIO-1 │
   └────────┴─────────┴────────┴──────┴──────────────┴──────────────┘
   ┌────────────────────────────────────────────────────────────────────────────────────────┐
   │ Expiration for latest version (Expiration)                                             │
   ├────────┬─────────┬────────┬─────────────────────┬────────────────┬─────────────────────┤
   │ ID     │ STATUS  │ PREFIX │ TAGS                │ DAYS TO EXPIRE │ EXPIRE DELETEMARKER │
   ├────────┼─────────┼────────┼─────────────────────┼────────────────┼─────────────────────┤
   │ rule-1 │ Enabled │ doc/   │ key1=val1&key2=val2 │             30 │ false               │
   └────────┴─────────┴────────┴─────────────────────┴────────────────┴─────────────────────┘
   ┌──────────────────────────────────────────────────────────────────────────────────┐
   │ Expiration for older versions (NoncurrentVersionExpiration)                      │
   ├────────┬─────────┬────────┬─────────────────────┬────────────────┬───────────────┤
   │ ID     │ STATUS  │ PREFIX │ TAGS                │ DAYS TO EXPIRE │ KEEP VERSIONS │
   ├────────┼─────────┼────────┼─────────────────────┼────────────────┼───────────────┤
   │ rule-1 │ Enabled │ doc/   │ key1=val1&key2=val2 │             15 │             0 │
   │ rule-2 │ Enabled │ logs/  │ -                   │              1 │             3 │
   └────────┴─────────┴────────┴─────────────────────┴────────────────┴───────────────┘

.. tab-set::

   .. tab-item:: 示例

      以下命令列出 ``myminio`` MinIO 部署中 ``mydata`` 存储桶的所有生命周期管理规则：

      .. code-block:: shell
         :class: copyable

         mc ilm ls myminio/mydata

   .. tab-item:: 语法

      :mc:`mc ilm ls` 命令的语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] ilm ls                        \
                          [--expiry | --transition]     \

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~

.. mc-cmd:: ALIAS
   :required:
   
   用于指定要列出对象生命周期管理规则的 MinIO 部署 :ref:`alias <alias>` 和存储桶完整路径。例如：

   .. code-block:: none

      mc ilm ls myminio/mydata


.. mc-cmd:: --expiry
   :optional:
   

   :mc:`mc ilm ls` 仅返回与生命周期规则过期相关的字段。

   与 :mc-cmd:`~mc ilm ls --transition` 互斥。

.. mc-cmd:: --transition
   :optional:

   :mc:`mc ilm ls` 仅返回与生命周期规则转换相关的字段。

   与 :mc-cmd:`~mc ilm ls --expiry` 互斥。


全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

列出存储桶生命周期管理规则
~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc:`mc ilm ls` 列出存储桶的生命周期管理规则：

.. code-block:: shell
   :class: copyable

   mc ilm ls ALIAS/PATH

- 将 :mc-cmd:`ALIAS <mc ilm ls ALIAS>` 替换为 S3 兼容主机的 :mc:`alias <mc alias>`。

- 将 :mc-cmd:`PATH <mc ilm ls ALIAS>` 替换为 S3 兼容主机上存储桶的路径。

行为
----

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
