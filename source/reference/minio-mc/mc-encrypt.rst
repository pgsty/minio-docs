==============
``mc encrypt``
==============

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc encrypt


描述
----

.. start-mc-encrypt-desc

:mc:`mc encrypt` 命令用于设置、更新或禁用存储桶默认的服务端加密（SSE）模式。
MinIO 会使用指定的 SSE 模式自动加密对象。

.. end-mc-encrypt-desc

子命令
------

:mc:`mc encrypt` 包含以下子命令：

.. list-table::
   :header-rows: 1
   :widths: 30 70
   :width: 100%

   * - 子命令
     - 描述

   * - :mc:`~mc encrypt clear`
     - .. include:: /reference/minio-mc/mc-encrypt-clear.rst
          :start-after: start-mc-encrypt-clear-desc
          :end-before: end-mc-encrypt-clear-desc

   * - :mc:`~mc encrypt info`
     - .. include:: /reference/minio-mc/mc-encrypt-info.rst
          :start-after: start-mc-encrypt-info-desc
          :end-before: end-mc-encrypt-info-desc

   * - :mc:`~mc encrypt set`
     - .. include:: /reference/minio-mc/mc-encrypt-set.rst
          :start-after: start-mc-encrypt-set-desc
          :end-before: end-mc-encrypt-set-desc

.. toctree::
   :titlesonly:
   :hidden:
   
   /reference/minio-mc/mc-encrypt-clear
   /reference/minio-mc/mc-encrypt-info
   /reference/minio-mc/mc-encrypt-set
