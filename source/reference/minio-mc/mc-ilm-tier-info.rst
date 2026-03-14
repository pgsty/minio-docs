.. _minio-mc-ilm-tier-info:

====================
``mc ilm tier info``
====================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc ilm tier info

描述
----

.. start-mc-ilm-tier-info-desc

:mc:`mc ilm tier info` 命令可输出某个层级或某个部署上所有层级的统计信息。

.. end-mc-ilm-tier-info-desc

所需权限
~~~~~~~~

MinIO 要求具备以下权限，且权限范围限定为你要创建生命周期管理规则的一个或多个存储桶。

- :policy-action:`s3:PutLifecycleConfiguration`
- :policy-action:`s3:GetLifecycleConfiguration`

此外，在为对象转换生命周期管理规则创建远程层级时，MinIO 还要求在对应集群上具备以下管理权限：

- :policy-action:`admin:SetTier`
- :policy-action:`admin:ListTier`

例如，以下策略授予在集群中任意存储桶上配置对象转换生命周期管理规则的权限：

.. literalinclude:: /extra/examples/LifecycleManagementAdmin.json
   :language: json
   :class: copyable

语法
----

该命令的语法如下：

.. tab-set::

   .. tab-item:: 示例

      以下示例输出 ``myminio`` 部署上名为 ``WARM-TIER`` 的现有远程层级的配置信息。
      
      .. code-block:: shell
         :class: copyable

          mc ilm tier info myminio WARM-TIER


   .. tab-item:: 语法
   
      该命令的语法如下：

      .. code-block:: shell
         :class: copyable

         mc ilm tier info TARGET TIER_NAME 

参数
~~~~

该命令接受以下参数：

.. mc-cmd:: TARGET
   :required:

   已配置 MinIO 部署的 :mc-cmd:`alias <mc alias>`，且目标层级存在于该部署上。
      
.. mc-cmd:: TIER_NAME
   :optional:

   要显示的现有远程层级名称。

   你**必须**使用全大写指定层级，例如 ``WARM_TIER``。

   如果未指定，MinIO 会列出该部署上所有现有层级的统计信息。
   

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

显示现有层级的统计信息
~~~~~~~~~~~~~~~~~~~~~~

以下示例显示 ``myminio`` 部署上 ``WARM-TIER`` 层级的统计信息。

.. code-block:: shell
   :class: copyable

   mc ilm tier info myminio WARM-TIER 

显示部署上所有现有层级的统计信息
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下示例显示 ``myminio`` 部署上所有现有层级的统计信息。

.. code-block:: shell
   :class: copyable

   mc ilm tier info myminio

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility

所需权限
--------

有关查看层级所需的权限，请参阅父命令中的 :ref:`required permissions <minio-mc-ilm-tier-permissions>`。
