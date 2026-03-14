.. _minio-mc-policy-links:

======================
``mc anonymous links``
======================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc anonymous links

语法
----

.. start-mc-anonymous-links-desc

:mc:`mc anonymous links` 获取用于匿名（即未认证或公开）访问存储桶的 HTTP URL。

.. end-mc-anonymous-links-desc

配置了匿名策略的存储桶允许客户端在无需
:ref:`认证 <minio-authentication-and-identity-management>` 的情况下访问存储桶内容，
并执行与指定策略一致的操作。

.. tab-set::

   .. tab-item:: 示例

      以下命令获取 ``myminio`` MinIO 部署上 ``mydata`` 存储桶的 HTTP URL：

      .. code-block:: shell
         :class: copyable

         mc anonymous links --recursive myminio/mydata

   .. tab-item:: 语法

      该命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] policy links   \
                          [--recursive]  \
                          ALIAS

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~

.. mc-cmd:: ALIAS

   *必需* 需要获取匿名存储桶策略的存储桶或存储桶前缀的完整路径。
   
   指定 MinIO 或其他 S3 兼容服务的 :ref:`alias <alias>`，*以及* 存储桶或
   存储桶前缀的完整路径。例如：

   .. code-block:: shell
            
      mc anonymous links public [FLAGS] play/mybucket

.. mc-cmd:: --recursive
   

   *可选* 递归获取 HTTP 链接。

全局参数
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

列出存储桶的匿名策略
~~~~~~~~~~~~~~~~~~

使用 :mc:`mc anonymous links` 列出存储桶的匿名策略：

.. code-block:: shell
   :class: copyable

   mc anonymous links ALIAS/PATH

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
