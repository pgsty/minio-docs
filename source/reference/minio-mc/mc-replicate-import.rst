.. _minio-mc-replicate-import:

=======================
``mc replicate import``
=======================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc replicate import

语法
----

.. start-mc-replicate-import-desc

:mc:`mc replicate import` 命令从 ``STDIN`` 为 MinIO 存储桶导入 JSON 格式的
:ref:`replication rules <minio-bucket-replication-serverside>`。

.. end-mc-replicate-import-desc

.. tab-set::

   .. tab-item:: 示例

      以下命令为 ``myminio`` MinIO 部署上的 ``mydata`` 存储桶导入复制配置：

      .. code-block:: shell
         :class: copyable

         mc replicate import myminio/mydata < mydata-replication.json

   .. tab-item:: 语法

      该命令的语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] import ALIAS

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~

.. mc-cmd:: ALIAS

   *必需*。MinIO 部署的 :ref:`alias <alias>`，以及要导入复制规则的存储桶或存储桶前缀的完整路径。例如：

   .. code-block:: none

      mc replicate import myminio/mybucket

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

导入现有复制规则
~~~~~~~~~~~~~~~~

使用 :mc:`mc replicate import` 导入存储桶复制规则：

.. code-block:: shell
   :class: copyable

   mc replicate import ALIAS/PATH < bucket-replication-rules.json

- 将 :mc-cmd:`ALIAS <mc replicate import ALIAS>` 替换为 MinIO 部署的
  :mc:`alias <mc alias>`。

- 将 :mc-cmd:`PATH <mc replicate import ALIAS>` 替换为存储桶或存储桶前缀路径。

行为
----

导入配置会覆盖现有规则
~~~~~~~~~~~~~~~~~~~~~~

:mc:`mc replicate import` 会将当前存储桶复制规则替换为导入的 JSON 配置中定义的规则。

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
