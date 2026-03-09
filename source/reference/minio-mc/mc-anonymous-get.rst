.. _minio-mc-policy-get:

====================
``mc anonymous get``
====================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc anonymous get

语法
----

.. start-mc-anonymous-get-desc

:mc:`mc anonymous get` 命令用于获取存储桶的匿名（即未认证或公共）访问
:ref:`策略 <minio-policy>`。

.. end-mc-anonymous-get-desc

配置了匿名策略的存储桶允许客户端在无需
:ref:`身份验证 <minio-authentication-and-identity-management>` 的情况下访问存储桶内容，
并执行与指定策略一致的操作。

要获取分配给存储桶的 :s3-docs:`JSON 策略 <using-iam-policies>`，
请使用 :mc-cmd:`mc anonymous get-json` 命令。

.. tab-set::

   .. tab-item:: 示例

      以下命令检索 ``myminio`` MinIO 部署上 ``mydata`` 存储桶的匿名访问策略：

      .. code-block:: shell
         :class: copyable

         mc anonymous get myminio/mydata

   .. tab-item:: 语法

      该命令具有以下语法：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] policy get ALIAS

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


参数
~~~~

.. mc-cmd:: ALIAS

   *必需* 命令要检索其匿名存储桶策略的存储桶或存储桶前缀完整路径。
   
   指定 MinIO 或其他 S3 兼容服务的 :ref:`别名 <alias>`，以及存储桶或存储桶前缀的
   完整路径。例如：

   .. code-block:: shell
            
      mc anonymous get public play/mybucket

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

获取存储桶匿名策略
~~~~~~~~~~~~~~~~~

使用 :mc:`mc anonymous get` 获取存储桶的匿名策略：

.. code-block:: shell
   :class: copyable

   mc anonymous get ALIAS/PATH

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
