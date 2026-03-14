.. _minio-mc-policy-set:
.. _minio-mc-anonymous-set:

====================
``mc anonymous set``
====================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc anonymous set

语法
----

.. start-mc-anonymous-set-desc

:mc:`mc anonymous set` 命令为存储桶设置匿名（即未认证或公开）访问
:ref:`策略 <minio-policy>`。

.. end-mc-anonymous-set-desc

配置了匿名策略的存储桶允许客户端在无需
:ref:`身份验证 <minio-authentication-and-identity-management>` 的情况下，
访问存储桶内容并执行与指定策略一致的操作。

要使用 IAM :s3-docs:`JSON 策略 <using-iam-policies>` 设置存储桶匿名策略，
请使用 :mc-cmd:`mc anonymous set-json` 命令。

.. tab-set::

   .. tab-item:: 示例

      以下命令为 ``myminio`` MinIO 部署上的多个存储桶设置匿名访问策略：

      .. code-block:: shell
         :class: copyable

         mc anonymous set upload myminio/uploads
         mc anonymous set download myminio/downloads
         mc anonymous set public myminio/public

      应用程序可在无需认证的情况下执行以下操作：

      - 向 ``myminio/uploads`` 和 ``myminio/public`` 执行 ``PUT`` 对象操作。
      - 从 ``myminio/downloads`` 和 ``myminio/public`` 执行 ``GET`` 对象操作。

   .. tab-item:: 语法

      该命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] policy set PERMISSION ALIAS

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


参数
~~~~

.. mc-cmd:: PERMISSION
      
   *必需* 要分配给指定 ``ALIAS`` 的策略名称。
   指定以下值之一：

   - ``none`` - 禁用对 ``ALIAS`` 的匿名访问。
   - ``download`` - 启用对 ``ALIAS`` 的仅下载访问。
   - ``upload`` - 启用对 ``ALIAS`` 的仅上传访问。
   - ``public`` - 启用对 ``ALIAS`` 的下载和上传访问。

.. mc-cmd:: ALIAS

   *必需* 要应用指定 :mc-cmd:`~mc anonymous set PERMISSION` 的存储桶或存储桶前缀的完整路径。
   
   指定 MinIO 或其他 S3 兼容服务的 :ref:`alias <alias>`，*以及* 存储桶或存储桶前缀的完整路径。例如：

   .. code-block:: shell

      mc anonymous set public play/mybucket

   指定存储桶前缀可仅对该前缀设置策略。例如，以下命令分别为
   ``mybucket/downloads`` 和 ``mybucket/uploads`` 前缀设置不同的匿名存储桶策略：

   .. code-block:: shell

      mc anonymous set download play/mybucket/downloads
      mc anonymous set upload play/mybucket/uploads

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

为存储桶设置匿名策略
~~~~~~~~~~~~~~~~~~~~

使用 :mc:`mc anonymous set` 为存储桶设置匿名策略：

.. code-block:: shell
   :class: copyable

   mc anonymous set POLICY ALIAS/PATH

- 将 :mc-cmd:`POLICY <mc anonymous set PERMISSION>` 替换为受支持的
  :mc-cmd:`permission <mc anonymous set PERMISSION>`。

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
