============
``mc quota``
============

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc quota

.. versionchanged:: RELEASE.2024-07-31T15-58-33Z

   ``mc quota`` 及其子命令已弃用。

说明
----

.. start-mc-quota-desc

:mc:`mc quota` 命令用于为存储桶配置、显示或移除配额限制。 

.. end-mc-quota-desc

对于已配置配额的存储桶，当其达到指定限制时（由 MinIO object scanner 判定），MinIO 会拒绝该存储桶后续的 ``PUT`` 请求。

每当 MinIO :ref:`object scanner <minio-lifecycle-management-scanner>` 扫描存储桶中待处理的 :ref:`object lifecycle transitions <minio-lifecycle-management>` 时，也会检查该存储桶是否超出已配置的配额。

.. admonition:: 配额执行并非即时生效
   :class: note

   存储桶配额的设计目的不是对存储桶大小施加严格的硬限制。
   如果存储桶在两次扫描之间超出配额，MinIO 仍会继续接受该存储桶的 ``PUT`` 请求，直到下一次扫描识别出配额违规 _之后_ 才会拒绝。


子命令
------

:mc:`mc quota` 包含以下子命令：

.. list-table::
   :header-rows: 1
   :widths: 30 70
   :width: 100%

   * - 子命令
     - 说明

   * - :mc:`~mc quota clear`
     - .. include:: /reference/deprecated/mc-quota-clear.rst
          :start-after: start-mc-quota-clear-desc
          :end-before: end-mc-quota-clear-desc

   * - :mc:`~mc quota info`
     - .. include:: /reference/deprecated/mc-quota-info.rst
          :start-after: start-mc-quota-info-desc
          :end-before: end-mc-quota-info-desc

   * - :mc:`~mc quota set`
     - .. include:: /reference/deprecated/mc-quota-set.rst
          :start-after: start-mc-quota-set-desc
          :end-before: end-mc-quota-set-desc

.. toctree::
   :titlesonly:
   :hidden:
   
   /reference/deprecated/mc-quota-clear
   /reference/deprecated/mc-quota-info
   /reference/deprecated/mc-quota-set
