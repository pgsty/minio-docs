============
``mc share``
============

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc share


说明
----

.. start-mc-share-desc

使用 :mc:`mc share` 命令管理预签名 URL，以便下载和上传 MinIO 存储桶中的对象。

.. end-mc-share-desc

子命令
------

:mc:`mc share` 包含以下子命令：

.. list-table::
   :header-rows: 1
   :widths: 30 70
   :width: 100%

   * - 子命令
     - 说明

   * - :mc:`~mc share download`
     - .. include:: /reference/minio-mc/mc-share-download.rst
          :start-after: start-mc-share-download-desc
          :end-before: end-mc-share-download-desc

   * - :mc:`~mc share list`
     - .. include:: /reference/minio-mc/mc-share-list.rst
          :start-after: start-mc-share-list-desc
          :end-before: end-mc-share-list-desc

   * - :mc:`~mc share upload`
     - .. include:: /reference/minio-mc/mc-share-upload.rst
          :start-after: start-mc-share-upload-desc
          :end-before: end-mc-share-upload-desc

.. toctree::
   :titlesonly:
   :hidden:
   
   /reference/minio-mc/mc-share-download
   /reference/minio-mc/mc-share-upload
   /reference/minio-mc/mc-share-list
