======================
``mc admin policy rm``
======================

.. default-domain:: minio

.. contents:: Table of Contents
   :local:
   :depth: 2

.. mc:: mc admin policy remove
.. mc:: mc admin policy rm

语法
------

.. start-mc-admin-policy-remove-desc

从目标 MinIO 部署中移除 IAM 策略。

.. end-mc-admin-policy-remove-desc

:mc:`mc admin policy remove` 命令与 :mc:`mc admin policy rm` 具有等效功能。

.. tab-set::

   .. tab-item:: 示例

      以下命令从 ``myminio`` MinIO 部署中移除名为 ``writeonly`` 的策略：

      .. code-block:: shell
         :class: copyable

         mc admin policy rm myminio writeonly

   .. tab-item:: 语法

      该命令的语法如下：

      .. code-block:: shell
         :class: copyable

         mc admin policy rm TARGET POLICYNAME 

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


参数
~~~~~~~~~~

:mc-cmd:`mc admin policy rm` 命令接受以下参数：

.. mc-cmd:: TARGET

   要移除策略的已配置 MinIO 部署的 :mc-cmd:`alias <mc alias>`。

.. mc-cmd:: POLICYNAME

   要移除的策略名称。
      
全局标志
~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
--------

移除名为 ``listbuckets`` 的策略。

.. code-block:: shell
   :class: copyable

   mc admin policy rm myminio listbuckets
