=================
``mc admin heal``
=================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc admin heal

说明
----

.. start-mc-admin-heal-desc

:mc-cmd:`mc admin heal` 命令会扫描受损或损坏的对象，并对这些对象执行自愈。  

.. end-mc-admin-heal-desc

:mc-cmd:`mc admin heal` 资源开销较大，通常不需要手动执行，即使在驱动器故障或数据损坏事件之后也是如此。 

作为正常运行的一部分，MinIO 会：

- 在每次 ``POST`` 或 ``GET`` 操作时，自动修复因静默位腐坏、驱动器故障或其他问题而受损的对象。 
- 使用 :ref:`scanner <minio-concepts-scanner>` 周期性执行后台对象自愈。
- 在更换驱动器后积极执行对象自愈。

有关 MinIO 如何执行对象自愈的更多详细信息，请参阅 :ref:`minio-concepts-healing`。

.. admonition:: 仅在 MinIO 部署上使用 ``mc admin``
   :class: note

   .. include:: /includes/facts-mc-admin.rst
      :start-after: start-minio-only
      :end-before: end-minio-only

语法
----

:mc-cmd:`mc admin heal` 使用以下语法：

.. code-block:: shell
   :class: copyable

   mc admin heal [FLAGS] TARGET             \
                         [--all-drives, -a] \
                         [--force]          \
                         [--verbose, -v]

:mc-cmd:`mc admin heal` 支持以下参数：

.. mc-cmd:: TARGET
   :required:

   执行对象自愈的存储桶或存储桶前缀的完整路径。 
   指定已配置的 MinIO 部署的 :mc-cmd:`alias <mc alias>` 作为该路径的前缀。 
   例如：

   .. code-block:: shell
      :class: copyable

      mc admin heal play/mybucket/myprefix

   如果 ``TARGET`` 存储桶或存储桶前缀存在活动的自愈扫描，命令将返回该扫描的状态。

.. mc-cmd:: --all-drives, -a
   :optional:

   选择所有驱动器并显示详细信息。

.. mc-cmd:: --force
   :optional:

   禁用警告提示。

.. mc-cmd:: --verbose, -v
   :optional:

   显示离线和故障驱动器的自愈信息。


.. _minio-concepts-healing-colors:

自愈颜色
--------

某些版本的 MinIO 使用颜色标识来区分不同自愈状态的对象。

.. versionchanged:: mc RELEASE.2024-11-17T19-35-25Z

颜色含义已更新。

- 绿色表示存储桶健康。
- 黄色表示存储桶在一个或多个驱动器上需要执行自愈。
- 红色表示一个或多个驱动器处于不健康状态。
- 灰色表示自愈状态不确定。
