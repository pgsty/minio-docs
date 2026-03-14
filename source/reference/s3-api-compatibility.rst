====================
S3 API 兼容性
====================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

本页面记录 MinIO 对象存储支持的 S3 API。
如需查看任意 API 的参考文档，请参阅 Amazon S3 的对应文档。

.. important::

   MinIO 强烈建议使用 :ref:`S3 兼容 SDK <minio-drivers>` 执行对象存储操作。

对象 API
--------

- :s3-api:`CopyObject <API_CopyObject.html>`
- :s3-api:`DeleteObject <API_DeleteObject.html>`
- :s3-api:`DeleteObjects <API_DeleteObjects.html>`
- :s3-api:`DeleteObjectTagging <API_DeleteObjectTagging.html>`
- :s3-api:`GetObject <API_GetObject.html>`
- :s3-api:`GetObjectAttributes <API_GetObjectAttributes.html>`
- :s3-api:`GetObjectTagging <API_GetObjectTagging.html>`
- :s3-api:`HeadObject <API_HeadObject.html>`
- :s3-api:`ListObjects <API_ListObjects.html>`
- :s3-api:`ListObjectsV2 <API_ListObjectsV2.html>`
- :s3-api:`ListObjectVersions <API_ListObjectVersions.html>`
- :s3-api:`PutObject <API_PutObject.html>`
- :s3-api:`PutObjectTagging <API_PutObjectTagging.html>`
- :s3-api:`RestoreObject <API_RestoreObject.html>`
- :s3-api:`SelectObjectContent <API_SelectObjectContent.html>`

对象锁定
~~~~~~~~

- :s3-api:`GetObjectRetention <API_GetObjectRetention.html>`
- :s3-api:`PutObjectRetention <API_PutObjectRetention.html>`
- :s3-api:`GetObjectLegalHold <API_GetObjectLegalHold.html>`
- :s3-api:`PutObjectLegalHold <API_PutObjectLegalHold.html>`
- :s3-api:`GetObjectLockConfiguration <API_GetObjectLockConfiguration.html>`
- :s3-api:`PutObjectLockConfiguration <API_PutObjectLockConfiguration.html>`

不支持的 API 对象端点
~~~~~~~~~~~~~~~~~~~~~

.. code-block:: text

   GetObjectAcl
   PutObjectAcl

多部分上传
~~~~~~~~~~

- :s3-api:`AbortMultipartUpload <API_AbortMultipartUpload.html>`
- :s3-api:`CompleteMultipartUpload <API_CompleteMultipartUpload.html>`
- :s3-api:`CreateMultipartUpload <API_CreateMultipartUpload.html>`
- :s3-api:`ListMultipartUploads <API_ListMultipartUploads.html>`
- :s3-api:`ListParts <API_ListParts.html>`
- :s3-api:`UploadPart <API_UploadPart.html>`
- :s3-api:`UploadPartCopy <API_UploadPartCopy.html>`

与 S3 API 在多部分上传方面的差异
+++++++++++++++++++++++++++++++

- ``ListMultipartUploads`` 需要使用精确的对象名称作为前缀。
- ``PutBucketLifecycle`` 不支持 ``AbortIncompleteMultipartUpload`` 生命周期动作。

存储桶 API
----------


- :s3-api:`CreateBucket <API_CreateBucket.html>`
- :s3-api:`DeleteBucket <API_DeleteBucket.html>`
- :s3-api:`DeleteBucketEncryption <API_DeleteBucketEncryption.html>`
- :s3-api:`DeleteBucketTagging <API_DeleteBucketTagging.html>`
- :s3-api:`GetBucketEncryption <API_GetBucketEncryption.html>`
- :s3-api:`GetBucketLocation <API_GetBucketLocation.html>`
- :s3-api:`GetBucketTagging <API_GetBucketTagging.html>`
- :s3-api:`GetBucketVersioning <API_GetBucketVersioning.html>`
- :s3-api:`HeadBucket <API_HeadBucket.html>`
- :s3-api:`ListBuckets <API_ListBuckets.html>`
- :s3-api:`ListDirectoryBuckets <API_ListDirectoryBuckets.html>`
- :s3-api:`PutBucketEncryption <API_PutBucketEncryption.html>`
- :s3-api:`PutBucketTagging <API_PutBucketTagging.html>`
- :s3-api:`PutBucketVersioning <API_PutBucketVersioning.html>`

存储桶复制
~~~~~~~~~~

- :s3-api:`GetBucketReplication <API_GetBucketReplication.html>`
- :s3-api:`PutBucketReplication <API_PutBucketReplication.html>`
- :s3-api:`DeleteBucketReplication <API_DeleteBucketReplication.html>`

存储桶生命周期
~~~~~~~~~~~~~~

- :s3-api:`GetBucketLifecycle <API_GetBucketLifecycle.html>`
- :s3-api:`GetBucketLifecycleConfiguration <API_GetBucketLifecycleConfiguration.html>`
- :s3-api:`PutBucketLifecycle <API_PutBucketLifecycle.html>`
- :s3-api:`PutBucketLifecycleConfiguration <API_PutBucketLifecycleConfiguration.html>`
- :s3-api:`DeleteBucketLifecycle <API_DeleteBucketLifecycle.html>`

存储桶通知
~~~~~~~~~~

- :s3-api:`GetBucketNotification <API_GetBucketNotification.html>`
- :s3-api:`GetBucketNotificationConfiguration <API_GetBucketNotificationConfiguration.html>`
- :s3-api:`PutBucketNotification <API_PutBucketNotification.html>`
- :s3-api:`PutBucketNotificationConfiguration <API_PutBucketNotificationConfiguration.html>`

存储桶策略
~~~~~~~~~~

- :s3-api:`GetBucketPolicy <API_GetBucketPolicy.html>`
- :s3-api:`GetBucketPolicyStatus <API_GetBucketPolicyStatus.html>`
- :s3-api:`PutBucketPolicy <API_PutBucketPolicy.html>`
- :s3-api:`DeleteBucketPolicy <API_DeleteBucketPolicy.html>`

不支持的 API 存储桶操作
~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: text

   GetBucketInventoryConfiguration
   PutBucketInventoryConfiguration
   DeleteBucketInventoryConfiguration
   PutBucketCors
   DeleteBucketCors
   GetBucketMetricsConfiguration
   PutBucketMetricsConfiguration
   DeleteBucketMetricsConfiguration
   PutBucketWebsite
   GetBucketLogging
   PutBucketLogging
   PutBucketAccelerateConfiguration
   DeleteBucketAccelerateConfiguration
   PutBucketRequestPayment
   DeleteBucketRequestPayment
   PutBucketAcl
   HeadBucketAcl
   GetPublicAccessBlock
   PutPublicAccessBlock
   DeletePublicAccessBlock
   GetBucketOwnershipControls
   PutBucketOwnershipControls
   DeleteBucketOwnershipControls
   GetBucketIntelligentTieringConfiguration
   PutBucketIntelligentTieringConfiguration
   ListBucketIntelligentTieringConfigurations
   DeleteBucketIntelligentTieringConfiguration
   GetBucketAnalyticsConfiguration
   PutBucketAnalyticsConfiguration
   ListBucketAnalyticsConfigurations
   DeleteBucketAnalyticsConfiguration
   CreateSession

MinIO 针对不支持的存储桶资源的替代方案
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

- 对于 ``BucketACL`` 或 ``ObjectACL`` 操作调用，请使用 :ref:`Policies <minio-policy>`。
- 不需要 ``BucketCORS`` 操作调用，因为默认情况下所有存储桶都已为所有 HTTP 动词启用 CORS。
- 对于 ``BucketWebsite`` 操作调用，请使用 ``caddy`` 或 ``nginx``。
- 对于 ``BucketAnalytics``、``BucketMetrics`` 或 ``BucketLogging`` 操作调用，请使用 :ref:`存储桶通知 <minio-bucket-notifications>`。
