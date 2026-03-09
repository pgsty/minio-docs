.. _minio-mc-admin-cluster-iam-import:

===============================
``mc admin cluster iam import``
===============================

.. default-domain:: minio

.. mc:: mc admin cluster iam import

描述
----

.. versionadded:: RELEASE.2022-06-17T02-52-50Z

.. start-mc-admin-cluster-iam-import-desc

:mc:`mc admin cluster iam import` 命令导入由 :mc:`mc admin cluster iam export` 命令创建的 :ref:`IAM <minio-authentication-and-identity-management>` 元数据。

.. end-mc-admin-cluster-iam-import-desc

您可以使用此命令手动恢复 MinIO 部署的 IAM 元数据设置。

.. versionadded:: mc RELEASE.2024-09-09T07-53-10Z

   该命令会输出导入结果，包括以下内容：

   - 按实体类型统计已导入的单个实体数量

   - 按策略导入目标的实体类型列出已导入策略

   - 导入失败的实体列表

.. tab-set::

   .. tab-item:: 示例

      以下命令将指定文件中的 IAM 元数据导入到 ``myminio`` 部署。
  
      .. code-block:: shell  
         :class: copyable 
  
         mc admin cluster iam import myminio ~/minio-metadata-backup/myminio-cluster.zip

   .. tab-item:: 语法

      命令语法如下：
  
      .. code-block:: shell  
         :class: copyable 
  
         mc [GLOBALFLAGS] admin cluster iam import  \
                                            ALIAS \
                                            IAM-METADATA.ZIP  
                                             
      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

从 :mc-release:`RELEASE.2023-05-04T18-10-16Z` 开始，:mc:`mc admin cluster iam import` 新增了对以尾随正斜杠结尾的别名 ``ALIAS/`` 的支持。
在此版本之前，如果提供尾随正斜杠，命令会执行失败。

参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   MinIO 部署的 :mc-cmd:`alias <mc alias>`。

.. mc-cmd:: IAM-METADATA.ZIP
   :required:

   要导入的 IAM 元数据文件路径。
   
   使用 :mc:`mc admin cluster iam export` 导出 IAM 元数据，以供此命令使用。

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals
