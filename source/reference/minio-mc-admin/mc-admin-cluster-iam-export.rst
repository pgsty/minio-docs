.. _minio-mc-admin-cluster-iam-export:

===============================
``mc admin cluster iam export``
===============================

.. default-domain:: minio

.. mc:: mc admin cluster iam export

描述
----

.. versionadded:: RELEASE.2022-06-26T18-51-48Z

.. start-mc-admin-cluster-iam-export-desc

:mc:`mc admin cluster iam export` 命令导出 :ref:`IAM <minio-authentication-and-identity-management>` 元数据，以供 :mc:`mc admin cluster iam import` 命令使用。

.. end-mc-admin-cluster-iam-export-desc

该命令将输出保存为 ``ALIAS-iam-metadata.zip``，其中 ``ALIAS`` 是 MinIO 部署的 :mc:`alias <mc admin cluster iam export ALIAS>`。

.. tab-set::

   .. tab-item:: EXAMPLE

      以下命令导出 ``myminio`` 部署的全部 IAM 元数据。
  
      .. code-block:: shell  
         :class: copyable 
  
         mc admin cluster iam export myminio

   .. tab-item:: SYNTAX

      命令语法如下：
  
      .. code-block:: shell  
         :class: copyable 
  
         mc [GLOBALFLAGS] admin cluster iam export ALIAS  \
                          [--output, -o <string>]
                                             
      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

从 :mc-release:`RELEASE.2023-05-04T18-10-16Z` 开始，:mc:`mc admin cluster iam export` 支持以尾随正斜杠结尾的别名 ``ALIAS/``。
在此版本之前，提供尾随正斜杠会导致命令执行失败。

参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   要导出 IAM 元数据的 MinIO 部署 :ref:`alias <alias>`。


.. mc-cmd:: --output, --o
   :optional:

   指定导出 IAM 数据时使用的自定义文件名和路径。

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

将集群的全部 IAM 元数据下载到 ZIP 文件
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下命令下载别名为 ``myminio`` 的集群全部 IAM 元数据，并将其保存到 ZIP 文件中。

.. code-block:: shell

   mc admin cluster iam export myminio

ZIP 文件命名为 ``<alias>-iam-info.zip``，其中 ``<alias>`` 是集群别名。
在上述示例中，文件名为 ``myminio-iam-info.zip``。

该文件位于当前活动目录中。

下载集群的全部 IAM 元数据并指定 ZIP 文件名称和路径
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下命令下载别名为 ``myminio`` 的集群全部 IAM 元数据，并将其保存为 ``/tmp/myminio-iam.zip`` ZIP 文件。

.. code-block:: shell

   mc admin cluster iam export myminio --output /tmp/myminio-iam.zip
