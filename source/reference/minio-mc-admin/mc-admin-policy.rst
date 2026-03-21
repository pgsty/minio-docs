===================
``mc admin policy``
===================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc admin policy

.. versionchanged:: mc RELEASE.2023-03-20T17-17-53Z

   以下命令已弃用：
   
   - ``mc admin policy add`` 请改用 :mc:`mc admin policy create`
   - ``mc admin policy set`` 请改用 :mc:`mc admin policy attach`
   - ``mc admin policy unset`` 请改用 :mc:`mc admin policy detach`
   - ``mc admin policy update`` 请改用 :mc:`~mc admin policy attach` 或 :mc:`~mc admin policy detach`

   新增以下命令：

   - :mc-cmd:`mc admin policy entities`

说明
----

.. start-mc-admin-policy-desc

:mc:`mc admin policy` 命令用于管理可与 :ref:`MinIO 基于策略的访问控制 <minio-policy>` (PBAC) 配合使用的策略。
MinIO PBAC 使用与 IAM 兼容的策略 JSON 文档来定义访问 MinIO 服务器资源的规则。

.. end-mc-admin-policy-desc

有关 MinIO PBAC 的完整文档（包括策略文档 JSON 结构与语法），请参阅 :ref:`minio-policy`。要管理使用 LDAP 认证的部署中的策略，请参阅 :mc:`mc idp ldap policy`。

子命令
------

:mc:`mc admin policy` 包含以下子命令：

.. list-table::
   :header-rows: 1
   :widths: 30 70
   :width: 100%

   * - 子命令
     - 说明

   * - :mc:`~mc admin policy attach`
     - .. include:: /reference/minio-mc-admin/mc-admin-policy-attach.rst
          :start-after: start-mc-admin-policy-attach-desc
          :end-before: end-mc-admin-policy-attach-desc

   * - :mc:`~mc admin policy create`
     - .. include:: /reference/minio-mc-admin/mc-admin-policy-create.rst
          :start-after: start-mc-admin-policy-create-desc
          :end-before: end-mc-admin-policy-create-desc

   * - :mc:`~mc admin policy detach`
     - .. include:: /reference/minio-mc-admin/mc-admin-policy-detach.rst
          :start-after: start-mc-admin-policy-detach-desc
          :end-before: end-mc-admin-policy-detach-desc

   * - :mc:`~mc admin policy entities`
     - .. include:: /reference/minio-mc-admin/mc-admin-policy-entities.rst
          :start-after: start-mc-admin-policy-entities-desc
          :end-before: end-mc-admin-policy-entities-desc

   * - :mc:`~mc admin policy info`
     - .. include:: /reference/minio-mc-admin/mc-admin-policy-info.rst
          :start-after: start-mc-admin-policy-info-desc
          :end-before: end-mc-admin-policy-info-desc

   * - :mc:`~mc admin policy ls`
     - .. include:: /reference/minio-mc-admin/mc-admin-policy-list.rst
          :start-after: start-mc-admin-policy-list-desc
          :end-before: end-mc-admin-policy-list-desc

   * - :mc:`~mc admin policy rm`
     - .. include:: /reference/minio-mc-admin/mc-admin-policy-remove.rst
          :start-after: start-mc-admin-policy-remove-desc
          :end-before: end-mc-admin-policy-remove-desc

.. toctree::
   :titlesonly:
   :hidden:
   
   /reference/minio-mc-admin/mc-admin-policy-attach
   /reference/minio-mc-admin/mc-admin-policy-create
   /reference/minio-mc-admin/mc-admin-policy-detach
   /reference/minio-mc-admin/mc-admin-policy-entities
   /reference/minio-mc-admin/mc-admin-policy-info
   /reference/minio-mc-admin/mc-admin-policy-list
   /reference/minio-mc-admin/mc-admin-policy-remove
