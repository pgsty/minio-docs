.. _minio-mc-admin-config:

===================
``mc admin config``
===================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc admin config

说明
----

.. start-mc-admin-config-desc

:mc:`mc admin config` 命令用于管理 :mc:`minio` 服务器的配置设置。

.. end-mc-admin-bucket-remote-desc

.. admonition:: 仅在 MinIO 部署上使用 ``mc admin``
   :class: note

   .. include:: /includes/facts-mc-admin.rst
      :start-after: start-minio-only
      :end-before: end-minio-only

示例
----

语法
----

.. mc-cmd:: set
   :fullpath:

   在 MinIO 部署上设置 :ref:`配置键 <minio-server-configuration-settings>`。
   由环境变量定义的配置会覆盖通过此命令定义的配置。

.. mc-cmd:: get
   :fullpath:

   获取 MinIO 部署上通过 `mc admin config set` 创建的 :ref:`配置键 <minio-server-configuration-settings>`。

.. mc-cmd:: export
   :fullpath:

   导出通过 `mc admin config set` 创建的所有配置设置。

.. mc-cmd:: history
   :fullpath:

   列出由 `mc admin config` 对配置键所做变更的历史记录。

   由环境变量定义的配置不会显示在其中。

.. mc-cmd:: import
   :fullpath:

   导入通过 `mc admin config export` 导出的配置设置。

.. mc-cmd:: reset
   :fullpath:

   将配置重置为默认值。
   在环境变量中定义的配置不受影响。

.. mc-cmd:: restore
   :fullpath:

   将配置键的更改回滚到历史中的先前时间点。

   不影响由环境变量定义的配置。

配置设置
--------

可用配置设置列表请参见 :ref:`minio-server-configuration-settings`。
