.. _minio-mc-policy-get-json:

=========================
``mc anonymous get-json``
=========================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc anonymous get-json

语法
------

.. start-mc-anonymous-get-json-desc

:mc:`mc anonymous get-json` 命令用于获取存储桶的匿名（即未经身份验证或公开）访问 :ref:`policies <minio-policy>`。

.. end-mc-anonymous-get-json-desc

配置了匿名策略的存储桶允许客户端在无需
:ref:`authentication <minio-authentication-and-identity-management>` 的情况下访问存储桶内容，并执行与指定策略一致的操作。

.. tab-set::

   .. tab-item:: EXAMPLE

      以下命令检索 ``myminio`` MinIO 部署上 ``mydata`` 存储桶的 JSON 格式匿名策略：

      .. code-block:: shell
         :class: copyable

         mc anonymous get-json myminio/mydata

   .. tab-item:: SYNTAX

      该命令使用以下语法：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] get-json ALIAS

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~~~~~~~

.. mc-cmd:: ALIAS

   *必需* 要获取匿名存储桶策略的存储桶或存储桶前缀的完整路径。

   指定 MinIO 或其他 S3 兼容服务的 :ref:`alias <alias>`，*以及* 存储桶或存储桶前缀的完整路径。例如：

   .. code-block:: shell

      mc anonymous get-json public play/mybucket

全局标志
~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
--------

获取存储桶的匿名策略
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc anonymous get-json` 获取存储桶的匿名策略：

.. code-block:: shell
   :class: copyable

   mc anonymous get-json ALIAS/PATH

- 将 :mc-cmd:`ALIAS <mc anonymous get-json ALIAS>` 替换为已配置 S3 兼容主机的 :mc-cmd:`alias <mc alias>`。

- 将 :mc-cmd:`PATH <mc anonymous get-json ALIAS>` 替换为目标存储桶。

行为
--------

S3 兼容性
~~~~~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
