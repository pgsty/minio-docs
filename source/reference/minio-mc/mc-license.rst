==============
``mc license``
==============

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc license


说明
----

.. start-mc-license-desc

:mc:`mc license` 命令用于管理 |SUBNET| 的集群注册。
可使用这些命令注册部署、显示集群当前许可证信息，或更新集群的许可证密钥。

.. end-mc-license-desc

子命令
------

:mc:`mc license` 包含以下子命令：

.. list-table::
   :header-rows: 1
   :widths: 30 70
   :width: 100%

   * - 子命令
     - 说明

   * - :mc:`~mc license info`
     - .. include:: /reference/minio-mc/mc-license-info.rst
          :start-after: start-mc-license-info-desc
          :end-before: end-mc-license-info-desc

   * - :mc:`~mc license register`
     - .. include:: /reference/minio-mc/mc-license-register.rst
          :start-after: start-mc-license-register-desc
          :end-before: end-mc-license-register-desc

   * - :mc:`~mc license update`
     - .. include:: /reference/minio-mc/mc-license-update.rst
          :start-after: start-mc-license-update-desc
          :end-before: end-mc-license-update-desc

.. toctree::
   :titlesonly:
   :hidden:
   
   /reference/minio-mc/mc-license-info
   /reference/minio-mc/mc-license-register
   /reference/minio-mc/mc-license-update
