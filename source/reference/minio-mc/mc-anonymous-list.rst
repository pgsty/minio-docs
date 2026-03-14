.. _minio-mc-policy-list:

=====================
``mc anonymous list``
=====================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc anonymous list

语法
----

.. start-mc-anonymous-list-desc

:mc:`mc anonymous list` 检索存储桶的所有匿名（即未经身份验证或公开）访问策略。

.. end-mc-anonymous-list-desc

具有匿名策略的存储桶允许客户端在无需
:ref:`身份验证 <minio-authentication-and-identity-management>` 的情况下访问存储桶内容，
并执行与指定策略一致的操作。

.. tab-set::

   .. tab-item:: EXAMPLE

      以下命令列出 ``myminio`` MinIO 部署中 ``mydata`` 存储桶的所有匿名访问策略：

      .. code-block:: shell
         :class: copyable

         mc anonymous list myminio/mydata

   .. tab-item:: SYNTAX

      该命令具有以下语法：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] ALIAS

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


参数
~~~~

.. mc-cmd:: ALIAS

   *必需* 要检索其匿名存储桶策略的存储桶或存储桶前缀完整路径。
   
   指定 MinIO 或其他 S3 兼容服务的 :ref:`alias <alias>`，*以及* 存储桶或存储桶前缀的完整路径。
   例如：

   .. code-block:: shell
            
      mc anonymous list public play/mybucket

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

列出存储桶的匿名策略
~~~~~~~~~~~~~~~~~~

使用 :mc:`mc anonymous list` 列出存储桶的匿名策略：

.. code-block:: shell
   :class: copyable

   mc anonymous list ALIAS/PATH

- 将 :mc-cmd:`ALIAS <mc anonymous get ALIAS>` 替换为已配置 S3 兼容主机的
  :mc-cmd:`alias <mc alias>`。

- 将 :mc-cmd:`PATH <mc anonymous get ALIAS>` 替换为目标存储桶。

行为
----

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
