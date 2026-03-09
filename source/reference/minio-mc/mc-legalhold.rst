================
``mc legalhold``
================

.. default-domain:: minio

.. contents:: Table of Contents
   :local:
   :depth: 2

.. mc:: mc legalhold


描述
----

.. start-mc-legalhold-desc

:mc:`mc legalhold` 命令用于为一个或多个对象设置、移除或获取 :ref:`object legal hold (WORM) <minio-object-locking-legalhold>` 配置。

.. end-mc-legalhold-desc

子命令
------

:mc:`mc legalhold` 包含以下子命令：

.. list-table::
   :header-rows: 1
   :widths: 30 70
   :width: 100%

   * - 子命令
     - 描述

   * - :mc:`~mc legalhold clear`
     - .. include:: /reference/minio-mc/mc-legalhold-clear.rst
          :start-after: start-mc-legalhold-clear-desc
          :end-before: end-mc-legalhold-clear-desc

   * - :mc:`~mc legalhold info`
     - .. include:: /reference/minio-mc/mc-legalhold-info.rst
          :start-after: start-mc-legalhold-info-desc
          :end-before: end-mc-legalhold-info-desc

   * - :mc:`~mc legalhold set`
     - .. include:: /reference/minio-mc/mc-legalhold-set.rst
          :start-after: start-mc-legalhold-set-desc
          :end-before: end-mc-legalhold-set-desc

.. toctree::
   :titlesonly:
   :hidden:
   
   /reference/minio-mc/mc-legalhold-clear
   /reference/minio-mc/mc-legalhold-info
   /reference/minio-mc/mc-legalhold-set
