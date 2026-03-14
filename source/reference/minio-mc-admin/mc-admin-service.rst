====================
``mc admin service``
====================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc admin service

说明
----

.. start-mc-admin-service-desc

:mc-cmd:`mc admin service` 命令可用于重启或解除冻结 MinIO 服务器。

.. end-mc-admin-service-desc

:mc-cmd:`mc admin service` 会同时影响目标部署中的 *所有* MinIO 服务器。
该命令会中断 MinIO 部署上正在进行的 API 操作。对某个部署执行此命令时请谨慎。

.. admonition:: ``mc admin`` 仅用于 MinIO 部署
   :class: note

   .. include:: /includes/facts-mc-admin.rst
      :start-after: start-minio-only
      :end-before: end-minio-only

示例
----

重启目标部署中的 MinIO 服务器
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. include:: /includes/play-alias-available.rst
   :start-after: myminio-alias
   :end-before: end-myminio-alias

.. code-block:: shell
   :class: copyable

   mc admin service restart myminio

恢复目标部署上的 S3 调用
~~~~~~~~~~~~~~~~~~~~~~~~

.. include:: /includes/play-alias-available.rst
   :start-after: myminio-alias
   :end-before: end-myminio-alias

.. code-block:: shell
   :class: copyable

   mc admin service unfreeze myminio

语法
----

:mc-cmd:`mc admin service` 的语法如下：

.. code-block:: shell
   :class: copyable

   mc admin service COMMAND [ARGUMENTS]

:mc-cmd:`mc admin service` 支持以下命令：

.. mc-cmd:: restart

   重启 MinIO 服务器。
   如有需要，命令可能会根据状态建议重启节点。

   :mc-cmd:`mc admin service restart` 的语法如下：

   .. code-block:: shell
      :class: copyable

      mc admin service restart ALIAS

   指定已配置 MinIO 部署的 :mc-cmd:`alias <mc alias>`。
   :mc-cmd:`~mc admin service restart` 会重启该部署中的 *所有* MinIO 服务器。

.. mc-cmd:: unfreeze

   恢复 MinIO 集群上的 S3 API 调用。

   :mc-cmd:`mc admin service unfreeze` 的语法如下：

   .. code-block:: shell
      :class: copyable

      mc admin service unfreeze ALIAS

   指定已配置 MinIO 部署的 :mc-cmd:`alias <mc alias>`。
