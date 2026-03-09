.. _minio-mc-batch-generate:

=====================
``mc batch generate``
=====================

.. default-domain:: minio

.. contents:: Table of Contents
   :local:
   :depth: 2

.. mc:: mc batch generate

.. versionchanged:: MinIO RELEASE.2022-10-08T20-11-00Z or later

语法
----

.. start-mc-batch-generate-desc

:mc:`mc batch generate` 命令会为指定作业类型创建一个基础的 YAML 格式模板文件。

.. end-mc-batch-generate-desc

MinIO 创建该文件后，请在你偏好的文本编辑器中打开并进一步自定义。
每个批处理文件中只能定义一个作业任务定义。

请参阅 :ref:`job types <minio-batch-job-types>` 了解可生成的受支持作业。

.. tab-set::

   .. tab-item:: EXAMPLE

      以下命令会为 ``myminio`` 别名下 ``mybucket`` 存储桶上的 replicate 作业创建一个基础 YAML 文件。

      .. code-block:: shell
         :class: copyable

         mc batch generate myminio replicate

   .. tab-item:: SYNTAX

      该命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] batch generate \
                                ALIAS   \
                                JOBTYPE

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~

.. mc-cmd:: ALIAS
   :required:
   
   用于生成 YAML 模板文件的 :ref:`alias <alias>`。
   指定的 ``alias`` 不会限制你可使用该生成文件的部署位置。
   
   例如：

   .. code-block:: none

      mc batch generate myminio replicate

.. mc-cmd:: JOBTYPE
   :required:
   
   要生成 YAML 文档的作业类型。
   
   支持以下取值：
   
   - :ref:`minio-mc-batch-generate-replicate-job`
   - :ref:`minio-mc-batch-generate-keyrotate-job`
   - :ref:`minio-mc-batch-generate-expire-job` (Added ``mc.RELEASE.2023-12-02T11-24-10Z``)

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

为 Replicate 作业类型生成 ``yaml`` 文件
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下命令会为 replicate 类型批处理作业生成 YAML 蓝图，并将文件命名为 ``replicate``，扩展名为 ``.yaml``：

.. code-block:: shell
   :class: copyable

   mc batch generate alias replicate > replicate.yaml

- 将 ``alias`` 替换为用于生成 yaml 文件的 :mc:`alias <mc alias>`。

- 将 ``replicate`` 替换为要生成 yaml 文件的作业类型。
 
  :mc:`mc batch` 支持 ``replicate`` 和 ``keyrotate`` 作业类型。 


S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility

.. _minio-batch-job-types:

作业类型
--------

:mc:`mc batch` 当前支持以下作业任务类型：

- :ref:`minio-mc-batch-generate-replicate-job`
  
  在两个 MinIO 部署之间复制对象。
  以批处理作业形式提供与 :ref:`bucket replication <minio-bucket-replication>` 类似的功能，而不是持续扫描功能。

- :ref:`minio-mc-batch-generate-keyrotate-job`

  .. versionadded:: MinIO RELEASE.2023-04-07T05-28-58Z 
  
  为 MinIO 部署中静态对象的 sse-s3 或 sse-kms 密钥执行轮换。

- :ref:`minio-mc-batch-generate-expire-job`

  .. versionadded:: MinIO RELEASE.2023-12-02T10-51-33Z

  依据与 :ref:`minio-lifecycle-management-create-expiry-rule` 类似的语义使对象过期。

.. _minio-mc-batch-generate-replicate-job:

``replicate``
~~~~~~~~~~~~~

你可以将以下示例配置作为构建自定义复制批处理作业的起点：

.. literalinclude:: /includes/code/replicate.yaml
   :language: yaml

有关各个键更完整的文档，请参阅 :ref:`minio-batch-framework-replicate-job-ref`。

.. _minio-mc-batch-generate-keyrotate-job:

``keyrotate``
~~~~~~~~~~~~~

你可以将以下示例配置作为构建自定义密钥轮换批处理作业的起点：

.. literalinclude:: /includes/code/keyrotate.yaml
   :language: yaml

有关各个键更完整的文档，请参阅 :ref:`minio-batch-framework-keyrotate-job-ref`。

.. _minio-mc-batch-generate-expire-job:

``expire``
~~~~~~~~~~

你可以将以下示例配置作为构建自定义过期批处理作业的起点：

.. literalinclude:: /includes/code/expire.yaml
   :language: yaml

有关各个键更完整的文档，请参阅 :ref:`minio-batch-framework-expire-job-ref`。
