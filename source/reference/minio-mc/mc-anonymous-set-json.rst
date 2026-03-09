.. _minio-mc-policy-set-json:

=========================
``mc anonymous set-json``
=========================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc anonymous set-json

语法
----

.. start-mc-anonymous-set-json-desc

:mc:`mc anonymous set-json` 命令使用 IAM :s3-docs:`JSON policy document <using-iam-policies>`
为存储桶设置匿名（即未认证或公开）访问 :ref:`policies <minio-policy>`。

.. end-mc-anonymous-set-json-desc

配置了匿名策略的存储桶允许客户端在无需
:ref:`authentication <minio-authentication-and-identity-management>` 的情况下访问存储桶内容，
并执行与指定策略一致的操作。

.. tab-set::

   .. tab-item:: 示例

      以下命令将 JSON 格式的匿名策略应用到 ``myminio`` MinIO 部署上的 ``mydata`` 存储桶：

      .. code-block:: shell
         :class: copyable

         mc anonymous set-json ~/mydata-anonymous.json myminio/mydata

   .. tab-item:: 语法

      命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] set-json POLICY ALIAS

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


参数
~~~~

.. mc-cmd:: POLICY
      
   *必需* 要分配给指定 ``ALIAS`` 的 JSON 格式策略文件路径。

.. mc-cmd:: ALIAS

   *必需* 存储桶或存储桶前缀的完整路径，命令会将指定的
   :mc-cmd:`~mc anonymous set-json POLICY` 应用到该路径。
   
   指定 MinIO 或其他 S3 兼容服务的 :ref:`alias <alias>`，*以及* 存储桶或存储桶前缀的完整路径。例如：

   .. code-block:: shell
            
      mc anonymous set-json public play/mybucket

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

为存储桶设置匿名策略
~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc anonymous set-json` 为存储桶设置匿名策略：

.. code-block:: shell
   :class: copyable

   mc anonymous set-json POLICY ALIAS/PATH

- 将 :mc-cmd:`POLICY <mc anonymous set-json POLICY>` 替换为受支持的
  :mc-cmd:`POLICY <mc anonymous set-json POLICY>`。

- 将 :mc-cmd:`ALIAS <mc anonymous set-json ALIAS>` 替换为已配置 S3 兼容主机的
  :mc-cmd:`alias <mc alias>`。

- 将 :mc-cmd:`PATH <mc anonymous set-json ALIAS>` 替换为目标存储桶。

移除存储桶的匿名策略
~~~~~~~~~~~~~~~~~~

使用 :mc:`mc anonymous set` 清除存储桶的匿名策略：

.. code-block:: shell
   :class: copyable

   mc anonymous set none ALIAS/PATH

- 将 :mc-cmd:`ALIAS <mc anonymous set ALIAS>` 替换为已配置 S3 兼容主机的
  :mc-cmd:`alias <mc alias>`。

- 将 :mc-cmd:`PATH <mc anonymous set ALIAS>` 替换为目标存储桶。

行为
----

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
