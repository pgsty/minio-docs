.. _minio-bucket-replication-requirements:

=========================================
设置存储桶复制的要求
=========================================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 1

.. _minio-bucket-replication-serverside-oneway-permissions:

存储桶复制使用规则将一个 MinIO 部署中的存储桶内容同步到远端 MinIO 部署中的存储桶。

复制可以通过以下任一方式完成：

- :ref:`Active-Passive <minio-bucket-replication-serverside-oneway>`
  符合条件的对象会从源存储桶复制到远端存储桶。
  远端存储桶上的任何更改都不会反向复制回来。
- :ref:`Active-Active <minio-bucket-replication-serverside-twoway>`
  任一存储桶中符合条件对象的更改都会以双向方式复制到另一个存储桶。
- :ref:`Multi-Site Active-Active <minio-bucket-replication-serverside-multi>`
  任何已设置存储桶复制的存储桶中符合条件对象的更改，都会复制到所有其他存储桶。

在设置这些复制配置之前，请确保满足以下前提条件。

设置存储桶复制所需的权限
------------------------------------------------------

.. include:: /includes/common-replication.rst
   :start-after: start-replication-required-permissions
   :end-before: end-replication-required-permissions

存储桶复制的对象加密设置需保持一致
----------------------------------------------------------

.. include:: /includes/common-replication.rst
   :start-after: start-replication-encrypted-objects
   :end-before: end-replication-encrypted-objects

存储桶复制要求使用 MinIO 部署
---------------------------------------------

.. include:: /includes/common-replication.rst
   :start-after: start-replication-minio-only
   :end-before: end-replication-minio-only

复制要求启用版本控制
-------------------------------

.. include:: /includes/common-replication.rst
   :start-after: start-replication-requires-versioning
   :end-before: end-replication-requires-versioning

对象锁定状态需与存储桶复制保持一致
-----------------------------------------------------

.. include:: /includes/common-replication.rst
   :start-after: start-replication-requires-object-locking
   :end-before: end-replication-requires-object-locking
