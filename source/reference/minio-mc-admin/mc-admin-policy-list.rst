======================
``mc admin policy ls``
======================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc admin policy ls
.. mc:: mc admin policy list

语法
------

.. start-mc-admin-policy-list-desc

列出目标 MinIO 部署上的所有策略。

.. end-mc-admin-policy-list-desc

:mc:`mc admin policy list` 命令与 :mc:`mc admin policy ls` 具有等效功能。

.. tab-set::

   .. tab-item:: 示例

      以下命令显示 :term:`alias` ``play`` 上当前存在的策略列表。

      .. code-block:: shell
         :class: copyable

         mc admin policy ls play

   .. tab-item:: 语法

      该命令的语法如下：

      .. code-block:: shell
         :class: copyable

         mc admin policy ls TARGET

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


参数
~~~~~~~~~~

:mc-cmd:`mc admin policy ls` 命令接受以下参数：

.. mc-cmd:: TARGET
   
   已配置 MinIO 部署的 :mc-cmd:`alias <mc alias>`，命令将从该部署列出可用策略。

全局标志
~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
--------

列出别名为 ``myminio`` 的部署上存在的策略。

.. code-block:: shell
   :class: copyable

   mc admin policy ls myminio

输出
~~~~~~

该命令返回的输出类似如下：

.. code-block:: shell

   readwrite
   writeonly
