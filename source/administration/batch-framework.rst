.. _minio-batch-framework:

===============
批处理框架
===============

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2



概述
----

MinIO 批处理框架允许使用 YAML 格式的作业定义文件（“batch file”）来创建、管理、监控和执行作业。
批处理作业直接在 MinIO 部署上运行，从而利用服务端处理能力，而不受运行 :ref:`MinIO Client <minio-client>` 的本地机器限制。

一个批处理文件定义一个作业任务。

启动后，MinIO 会开始处理该作业。
完成所需时间取决于部署可用的资源。

如果作业的任何部分失败，MinIO 会按照作业定义中指定的次数上限重试该作业。

MinIO 批处理框架支持以下作业类型：

.. list-table:: 
   :header-rows: 1
   :widths: 30 70
   :width: 100%   

   * - 作业类型
     - 说明

   * - :ref:`replicate <minio-batch-framework-replicate-job>`
     - 执行一次性复制流程，将数据从一个 MinIO 位置复制到另一个 MinIO 位置。

   * - :ref:`keyrotate <minio-batch-framework-keyrotate-job>`
     - 执行一次性流程，轮换对象上的 :ref:`sse-s3 or sse-kms <minio-sse-data-encryption>` 加密密钥。

   * - :ref:`expire <minio-batch-framework-expire-job>`
     - 对存储桶中的对象执行一次性立即过期操作。

MinIO 批处理 CLI
-----------------

- 安装 :ref:`MinIO Client <minio-client>`
- 为 MinIO 部署定义一个 :mc:`alias <mc alias set>`

:mc:`mc batch` 命令包括：

.. list-table::
   :widths: 30 70
   :width: 90%

   * - :mc:`mc batch generate`
     - .. include:: /reference/minio-mc/mc-batch-generate.rst
          :start-after: start-mc-batch-generate-desc
          :end-before: end-mc-batch-generate-desc
   * - :mc:`mc batch start`
     - .. include:: /reference/minio-mc/mc-batch-start.rst
          :start-after: start-mc-batch-start-desc
          :end-before: end-mc-batch-start-desc
   * - :mc:`mc batch list`
     - .. include:: /reference/minio-mc/mc-batch-list.rst
          :start-after: start-mc-batch-list-desc
          :end-before: end-mc-batch-list-desc
   * - :mc:`mc batch status`
     - .. include:: /reference/minio-mc/mc-batch-status.rst
          :start-after: start-mc-batch-status-desc
          :end-before: end-mc-batch-status-desc
   * - :mc:`mc batch describe`
     - .. include:: /reference/minio-mc/mc-batch-describe.rst
          :start-after: start-mc-batch-describe-desc
          :end-before: end-mc-batch-describe-desc
   * - :mc:`mc batch cancel`
     - .. include:: /reference/minio-mc/mc-batch-cancel.rst
          :start-after: start-mc-batch-cancel-desc
          :end-before: end-mc-batch-cancel-desc

.. _minio-batch-framework-access:

``mc batch`` 的访问权限
-----------------------

每个批处理作业都使用批处理定义中指定的凭证执行。
批处理作业能否成功，取决于这些凭证是否具有执行所有请求操作所需的适当 :ref:`权限 <minio-policy>`。

执行批处理作业的用户必须具有以下权限。
也可以通过阻止或限制对这些操作的访问，来限制用户使用这些功能：

``admin:ListBatchJobs``
  授予用户查看当前正在处理的批处理作业的能力。

``admin:DescribeBatchJobs``
  授予用户查看当前正在处理的批处理作业定义详情的能力。

``admin:StartBatchJob``
  授予用户启动批处理作业的能力。
  该作业还可能受到其用于访问源部署或目标部署的凭证进一步限制。

``admin:CancelBatchJob``
  允许用户停止当前正在进行的批处理作业。

可以将这些操作中的任意一个单独分配给用户，也可以任意组合分配。

内置的 ``ConsoleAdmin`` 策略包含执行所有这些类型批处理作业操作所需的充分访问权限。

.. _minio-batch-local:

``Local`` 部署
--------------

可通过向 :mc:`mc batch` 命令传递一个 ``alias``，对特定部署运行批处理作业。
命令中指定的部署会在该批处理作业的上下文中成为 ``local`` 部署。

.. toctree::
   :titlesonly:
   :hidden:
   
   /administration/batch-framework-job-replicate
   /administration/batch-framework-job-keyrotate
   /administration/batch-framework-job-expire
