==================
MinIO 管理客户端
==================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 1

.. mc:: mc admin

MinIO 客户端 :mc-cmd:`mc` 命令行工具提供 :mc:`mc admin` 命令，
用于在 MinIO 部署上执行管理任务。

虽然 :mc-cmd:`mc` 支持任意兼容 S3 的服务，
:mc:`mc admin` *仅* 支持 MinIO 部署。

:mc:`mc admin` 语法如下：

.. code-block:: shell

   mc admin [FLAGS] COMMAND [ARGUMENTS]

命令速查
--------

下表列出了 :mc:`mc admin` 命令：

.. list-table::
   :header-rows: 1
   :widths: 40 60
   :width: 100%

   * - 命令
     - 说明

   * - :mc:`mc admin accesskey`
     - .. include:: /reference/minio-mc-admin/mc-admin-accesskey.rst
          :start-after: start-mc-admin-accesskey-desc
          :end-before: end-mc-admin-accesskey-desc

   * - :mc:`mc admin cluster bucket`
     - .. include:: /reference/minio-mc-admin/mc-admin-cluster-bucket.rst
          :start-after: start-mc-admin-cluster-bucket-desc
          :end-before: end-mc-admin-cluster-bucket-desc

   * - :mc:`mc admin cluster iam`
     - .. include:: /reference/minio-mc-admin/mc-admin-cluster-iam.rst
          :start-after: start-mc-admin-cluster-iam-desc
          :end-before: end-mc-admin-cluster-iam-desc

   * - :mc-cmd:`mc admin decommission`
     - .. include:: /reference/minio-mc-admin/mc-admin-decommission.rst
          :start-after: start-mc-admin-decommission-desc
          :end-before: end-mc-admin-decommission-desc

   * - :mc:`mc admin group`
     - .. include:: /reference/minio-mc-admin/mc-admin-group.rst
          :start-after: start-mc-admin-group-desc
          :end-before: end-mc-admin-group-desc

   * - :mc-cmd:`mc admin heal`
     - .. include:: /reference/minio-mc-admin/mc-admin-heal.rst
          :start-after: start-mc-admin-heal-desc
          :end-before: end-mc-admin-heal-desc
  
   * - :mc-cmd:`mc admin info`
     - .. include:: /reference/minio-mc-admin/mc-admin-info.rst
          :start-after: start-mc-admin-info-desc
          :end-before: end-mc-admin-info-desc

   * - :mc-cmd:`mc admin kms key`
     - .. include:: /reference/minio-mc-admin/mc-admin-kms-key.rst
          :start-after: start-mc-admin-kms-key-desc
          :end-before: end-mc-admin-kms-key-desc

   * - :mc-cmd:`mc admin logs`
     - .. include:: /reference/minio-mc-admin/mc-admin-logs.rst
          :start-after: start-mc-admin-logs-desc
          :end-before: end-mc-admin-logs-desc

   * - :mc:`mc admin policy`
     - .. include:: /reference/minio-mc-admin/mc-admin-policy.rst
          :start-after: start-mc-admin-policy-desc
          :end-before: end-mc-admin-policy-desc

   * - :mc-cmd:`mc admin prometheus`
     - .. include:: /reference/minio-mc-admin/mc-admin-prometheus.rst
          :start-after: start-mc-admin-prometheus-desc
          :end-before: end-mc-admin-prometheus-desc

   * - :mc-cmd:`mc admin rebalance`
     - .. include:: /reference/minio-mc-admin/mc-admin-rebalance.rst
          :start-after: start-mc-admin-rebalance-desc
          :end-before: end-mc-admin-rebalance-desc

   * - :mc-cmd:`mc admin replicate`
     - .. include:: /reference/minio-mc-admin/mc-admin-replicate.rst
          :start-after: start-mc-admin-replicate-desc
          :end-before: end-mc-admin-replicate-desc

   * - :mc-cmd:`mc admin scanner`
     - .. include:: /reference/minio-mc-admin/mc-admin-scanner.rst
          :start-after: start-mc-admin-scanner-desc
          :end-before: end-mc-admin-scanner-desc

   * - :mc-cmd:`mc admin service`
     - .. include:: /reference/minio-mc-admin/mc-admin-service.rst
          :start-after: start-mc-admin-service-desc
          :end-before: end-mc-admin-service-desc

   * - :mc-cmd:`mc admin trace`
     - .. include:: /reference/minio-mc-admin/mc-admin-trace.rst
          :start-after: start-mc-admin-trace-desc
          :end-before: end-mc-admin-trace-desc

   * - :mc-cmd:`mc admin update`
     - .. include:: /reference/minio-mc-admin/mc-admin-update.rst
          :start-after: start-mc-admin-update-desc
          :end-before: end-mc-admin-update-desc

   * - :mc:`mc admin user`
     - .. include:: /reference/minio-mc-admin/mc-admin-user.rst
          :start-after: start-mc-admin-user-desc
          :end-before: end-mc-admin-user-desc

.. _mc-admin-install:

安装
----

.. include:: /includes/minio-mc-installation.rst

快速开始
--------

开始此流程前，请确保主机已 :ref:`安装 <mc-admin-install>` :mc:`mc`。

.. important::

   以下示例会临时禁用 bash 历史记录，以降低认证凭据明文泄露的风险。
   这是一项基础安全措施，无法缓解所有可能的攻击向量。请遵循你所用操作系统的
   安全最佳实践，在命令行中输入敏感信息。

使用 :mc:`mc alias set` 命令将该部署添加到 :program:`mc` 配置中。

.. code-block:: shell
   :class: copyable

   bash +o history
   mc config host add <ALIAS> <ENDPOINT> ACCESS_KEY SECRET_KEY
   bash -o history

将各参数替换为所需值。仅指定 ``mc config host add`` 命令会启动输入提示，
用于录入所需值。

使用 :mc-cmd:`mc admin info` 命令测试与新添加 MinIO 部署的连接：

.. code-block:: shell
   :class: copyable

   mc admin info <ALIAS>

全局选项
--------

:mc:`mc admin` 支持与 :mc-cmd:`mc` 相同的全局选项。
参见 :ref:`minio-mc-global-options`。

.. toctree::
   :titlesonly:
   :hidden:
   :glob:

   /reference/minio-mc-admin/mc-admin-accesskey
   /reference/minio-mc-admin/mc-admin-cluster-bucket
   /reference/minio-mc-admin/mc-admin-cluster-iam
   /reference/minio-mc-admin/mc-admin-config
   /reference/minio-mc-admin/mc-admin-decommission
   /reference/minio-mc-admin/mc-admin-group
   /reference/minio-mc-admin/mc-admin-heal
   /reference/minio-mc-admin/mc-admin-info
   /reference/minio-mc-admin/mc-admin-kms-key
   /reference/minio-mc-admin/mc-admin-logs
   /reference/minio-mc-admin/mc-admin-policy
   /reference/minio-mc-admin/mc-admin-prometheus
   /reference/minio-mc-admin/mc-admin-rebalance
   /reference/minio-mc-admin/mc-admin-replicate
   /reference/minio-mc-admin/mc-admin-scanner
   /reference/minio-mc-admin/mc-admin-service
   /reference/minio-mc-admin/mc-admin-trace
   /reference/minio-mc-admin/mc-admin-update
   /reference/minio-mc-admin/mc-admin-user
 
