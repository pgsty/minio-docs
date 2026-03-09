================
``mc anonymous``
================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc anonymous


说明
----

.. start-mc-anonymous-desc

:mc:`mc anonymous` 命令支持为存储桶及其内容设置或移除匿名 :ref:`policies <minio-policy>`。
配置了匿名策略的存储桶允许公开访问，客户端无需进行 :ref:`authentication <minio-authentication-and-identity-management>` 即可执行策略授予的任意操作。

.. end-mc-anonymous-desc

子命令
------

:mc-cmd:`mc anonymous` 包含以下子命令：

.. list-table::
   :header-rows: 1
   :widths: 30 70
   :width: 100%

   * - 子命令
     - 说明

   * - :mc:`~mc anonymous get`
     - .. include:: /reference/minio-mc/mc-anonymous-get.rst
          :start-after: start-mc-anonymous-get-desc
          :end-before: end-mc-anonymous-get-desc

   * - :mc:`~mc anonymous get-json`
     - .. include:: /reference/minio-mc/mc-anonymous-get-json.rst
          :start-after: start-mc-anonymous-get-json-desc
          :end-before: end-mc-anonymous-get-json-desc

   * - :mc:`~mc anonymous links`
     - .. include:: /reference/minio-mc/mc-anonymous-links.rst
          :start-after: start-mc-anonymous-links-desc
          :end-before: end-mc-anonymous-links-desc

   * - :mc:`~mc anonymous list`
     - .. include:: /reference/minio-mc/mc-anonymous-list.rst
          :start-after: start-mc-anonymous-list-desc
          :end-before: end-mc-anonymous-list-desc

   * - :mc:`~mc anonymous set`
     - .. include:: /reference/minio-mc/mc-anonymous-set.rst
          :start-after: start-mc-anonymous-set-desc
          :end-before: end-mc-anonymous-set-desc

   * - :mc:`~mc anonymous set-json`
     - .. include:: /reference/minio-mc/mc-anonymous-set-json.rst
          :start-after: start-mc-anonymous-set-json-desc
          :end-before: end-mc-anonymous-set-json-desc

.. toctree::
   :titlesonly:
   :hidden:
   
   /reference/minio-mc/mc-anonymous-set
   /reference/minio-mc/mc-anonymous-get
   /reference/minio-mc/mc-anonymous-list
   /reference/minio-mc/mc-anonymous-links
   /reference/minio-mc/mc-anonymous-get-json
   /reference/minio-mc/mc-anonymous-set-json
