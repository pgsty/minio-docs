.. _minio-mc-ilm-rule-ls:

==================
``mc ilm rule ls``
==================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc ilm rule list
.. mc:: mc ilm rule ls

.. versionchanged:: RELEASE.2022-12-24T15-21-38Z

   ``mc ilm rule ls`` 替代了 ``mc ilm ls``。

.. versionchanged:: RELEASE.2023-05-26T23-31-54Z

   ``mc ilm rule ls --json`` 的输出在 ``updateAt`` 中包含策略修改时间。
   
语法
----

.. start-mc-ilm-rule-ls-desc

:mc:`mc ilm rule ls` 命令以表格格式汇总 MinIO 存储桶上配置的所有对象生命周期管理规则。

.. end-mc-ilm-rule-ls-desc

:mc:`mc ilm rule list` 命令与 :mc:`mc ilm rule ls` 的功能等效。

.. tab-set::

   .. tab-item:: EXAMPLE

      以下命令列出 ``myminio`` MinIO 部署中 ``mydata`` 存储桶的所有生命周期管理规则：

      .. code-block:: shell
         :class: copyable

         mc ilm rule ls myminio/mydata

      命令输出可能类似如下：

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

   .. tab-item:: SYNTAX

      :mc:`mc ilm rule ls` 命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] ilm rule ls     \
                          [--expiry]      \
                          [--transition]

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~

.. mc-cmd:: ALIAS
   :required:
   
   要列出对象生命周期管理规则的 MinIO 部署中，存储桶对应的 :ref:`alias <alias>` 和完整路径。
   例如：

   .. code-block:: none

      mc ilm rule ls myminio/mydata


.. mc-cmd:: --expiry
   :optional:
   
   :mc:`mc ilm rule ls` 仅返回与生命周期规则过期相关的字段。

   与 :mc-cmd:`~mc ilm rule ls --transition` 互斥。

.. mc-cmd:: --transition
   :optional:

   :mc:`mc ilm rule ls` 仅返回与生命周期规则转移相关的字段。

   与 :mc-cmd:`~mc ilm rule ls --expiry` 互斥。


全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

列出存储桶生命周期管理规则
~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc:`mc ilm rule ls` 列出存储桶的生命周期管理规则：

.. code-block:: shell
   :class: copyable

   mc ilm rule ls ALIAS/PATH

- 将 :mc-cmd:`ALIAS <mc ilm rule ls ALIAS>` 替换为 S3 兼容主机的 :mc:`alias <mc alias>`。

- 将 ``PATH`` 替换为 S3 兼容主机上该存储桶的路径。

显示策略修改时间
~~~~~~~~~~~~~~~~

使用带有 :option:`--json <mc.--json>` 的 :mc:`mc ilm rule ls` 显示存储桶策略上次更新时间。

.. code-block:: shell
   :class: copyable

   mc ilm rule ls ALIAS/PATH --json

- 将 :mc-cmd:`ALIAS <mc ilm rule ls ALIAS>` 替换为 S3 兼容主机的 :mc:`alias <mc alias>`。

- 将 ``PATH`` 替换为 S3 兼容主机上该存储桶的路径。

JSON 输出中的 ``updateAt`` 属性包含该策略更新的日期和时间。

输出类似如下：

.. code-block:: shell

   {
    "status": "success",
    "target": "myminio/mybucket",
    "config": {
     "Rules": [
      {
       "Expiration": {
        "Days": 30
       },
       "ID": "ci1o2mg0sko6f1r3krv0",
       "Status": "Enabled"
      }
     ]
    },
    "updatedAt": "2023-06-09T19:45:30Z"
   }



所需权限
--------

有关列出规则所需的权限，请参阅父命令中的 :ref:`required permissions <minio-mc-ilm-rule-permissions>`。


行为
----

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
