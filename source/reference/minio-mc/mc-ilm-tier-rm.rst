.. _minio-mc-ilm-tier-rm:

==================
``mc ilm tier rm``
==================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc ilm tier remove
.. mc:: mc ilm tier rm

描述
----

.. start-mc-ilm-tier-rm-desc

:mc:`mc ilm tier rm` 命令用于移除尚未用于转换任何对象的远程层。

.. end-mc-ilm-tier-rm-desc

:mc:`mc ilm tier remove` 命令与 :mc:`mc ilm tier rm` 具有等效功能。

.. note::

   一旦某个层已经转换过对象，则无法将其移除。

所需权限
~~~~~~~~

MinIO 要求具备以下权限，且权限范围限定为你要创建生命周期管理规则的一个或多个存储桶。

- :policy-action:`s3:PutLifecycleConfiguration`
- :policy-action:`s3:GetLifecycleConfiguration`

MinIO 还要求在集群上具备以下管理权限，该集群用于为对象转换生命周期管理规则创建远程层：

- :policy-action:`admin:SetTier`
- :policy-action:`admin:ListTier`

例如，以下策略提供了在集群中任意存储桶上配置对象转换生命周期管理规则的权限：

.. literalinclude:: /extra/examples/LifecycleManagementAdmin.json
   :language: json
   :class: copyable

语法
----

该命令具有以下语法：

.. tab-set::

   .. tab-item:: 示例

      以下示例在 ``myminio`` 部署上移除名为 ``WARM-TIER`` 的现有远程层。
      没有对象被转换到 ``WARM-TIER`` 层。
      
      .. code-block:: shell
         :class: copyable

          mc ilm tier rm myminio WARM-TIER


   .. tab-item:: 语法

      该命令具有以下语法：

      .. code-block:: shell
         :class: copyable

         mc ilm tier info TARGET TIER_NAME

参数
~~~~

该命令接受以下参数：

.. mc-cmd:: TARGET
   :required:

   目标层所在的已配置 MinIO 部署的 :mc-cmd:`alias <mc alias>`。

.. mc-cmd:: TIER_NAME
   :required:

   要移除的现有远程层名称。

   你**必须**使用全大写指定该层，例如 ``WARM_TIER``。

   不能有任何对象已转换到该层。


全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility

所需权限
--------

有关移除层所需的权限，请参阅父命令中的 :ref:`required permissions <minio-mc-ilm-tier-permissions>`。
