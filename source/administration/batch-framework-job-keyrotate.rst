.. _minio-batch-framework-keyrotate-job:

============
批量密钥轮换
============


.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. versionadded:: MinIO RELEASE.2023-04-07T05-28-58Z

MinIO Batch Framework 允许你使用 YAML 格式的作业定义文件（“批处理文件”）来创建、管理、监控和执行作业。
批处理作业直接在 MinIO 部署上运行，以利用服务端处理能力，而不受运行 :ref:`MinIO Client <minio-client>` 的本地机器限制。

``keyrotate`` 批处理作业类型会为 MinIO 部署上的加密对象轮换 :ref:`sse-s3 or sse-kms keys <minio-sse-data-encryption>`。

YAML 配置支持按创建日期、标签、元数据或 kms key 进行过滤，将密钥轮换限制在特定对象集合上。
你还可以定义重试次数，或设置通知 endpoint 和 token。

.. _minio-batch-framework-keyrotate-job-ref:

密钥轮换批处理作业参考
----------------------

.. versionadded:: MinIO RELEASE.2023-04-07T05-28-58Z 

使用 ``keyrotate`` 作业类型创建批处理作业，以轮换加密对象的 :ref:`sse-s3 or sse-kms keys <minio-sse-data-encryption>`。

必填字段
~~~~~~~~

  .. list-table::
     :widths: 25 75
     :width: 100%

     * - ``type:`` 
       - ``sse-s3`` 或 ``sse-kms`` 之一。
     * - ``key:`` 
       - 仅用于 ``sse-kms`` 类型。
         用于解封密钥保管库的密钥。
   
可选字段
~~~~~~~~

对于**基于标志的过滤条件**

.. list-table::
   :widths: 25 75
   :width: 100%

   * - ``newerThan:`` 
     - 以 ``#d#h#s`` 格式表示时长的字符串。
       
       仅为比指定时长更新的对象轮换密钥。
       例如，``7d``、``24h``、``5d12h30s`` 都是有效字符串。
   * - ``olderThan:`` 
     - 以 ``#d#h#s`` 格式表示时长的字符串。
       
       仅为比指定时长更旧的对象轮换密钥。
   * - ``createdAfter:`` 
     - 采用 ``YYYY-MM-DDTHH:MM:SSZ`` :rfc:`RFC3339 <3339>` 日期时间格式的日期。
  
       仅为在该日期之后创建的对象轮换密钥。
   * - ``createdBefore:`` 
     - 采用 ``YYYY-MM-DDTHH:MM:SSZ`` :rfc:`RFC3339 <3339>` 日期时间格式的日期。
       
       仅为在该日期之前创建的对象轮换密钥。
   * - ``context:``
     - 仅用于 ``sse-kms`` 类型。
       执行操作时使用的上下文。
   * - ``tags:``
     - 仅为标签与指定 ``key:`` 和 ``value:`` 匹配的对象轮换密钥。  
   * - ``metadata:``
     - 仅为元数据与指定 ``key:`` 和 ``value:`` 匹配的对象轮换密钥。  
   * - ``kmskey:``
     - 仅为 KMS key-id 与指定值匹配的对象轮换密钥。  
       这仅适用于 ``sse-kms`` 类型。 

对于**通知**

.. list-table::
   :widths: 25 75
   :width: 100%

   * - ``endpoint:`` 
     - 用于发送通知事件的预定义 endpoint。
   * - ``token:`` 
     - 用于访问 ``endpoint`` 的可选 JSON Web Token (JWT)。

对于**重试**

如果作业被中断，你可以定义最大重试次数。
对于每次重试，你还可以定义两次尝试之间的等待时间。

.. list-table::
   :widths: 25 75
   :width: 100%

   * - ``attempts:`` 
     - 在放弃之前完成批处理作业的尝试次数。
   * - ``delay:`` 
     - 每次尝试之间的等待时长。

``keyrotate`` 作业类型的 YAML 描述文件示例
----------------------------------------------

使用 :mc:`mc batch generate` 创建一个基础的 ``keyrotate`` 批处理作业，以便进一步自定义：

.. literalinclude:: /includes/code/keyrotate.yaml
   :language: yaml
