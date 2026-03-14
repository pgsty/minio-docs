===================
``mc share ls``
===================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc share list
.. mc:: mc share ls

语法
-----------

.. start-mc-share-list-desc

:mc:`mc share ls` 命令会显示由 :mc:`mc share upload` 或
:mc:`mc share download` 生成的所有未过期预签名 URL。

.. end-mc-share-list-desc

:mc:`mc share list` 命令与 :mc:`mc share ls` 的功能等效。

应用程序可以执行 ``PUT`` 以从该 URL 获取对象。

有关可共享对象 URL 的更多信息，请参阅 Amazon S3 文档中的
:aws-docs:`Pre-Signed URLs
<AmazonS3/latest/dev/ShareObjectPreSignedURL.html>`.

.. tab-set::

   .. tab-item:: 示例

      以下命令分别列出 ``myminio`` MinIO 部署中 ``mydata`` 存储桶的
      所有上传和下载预签名 URL：

      .. code-block:: shell
         :class: copyable

         mc share ls upload myminio/mydata
         mc share ls download myminio/mydata

   .. tab-item:: 语法

      该命令的语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] share list           \
                          [download | upload]  \
                          ALIAS

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~~~~~~~

.. mc-cmd:: download

   *必需* 列出所有未过期的预签名下载（``GET``）URL。

   与 :mc:`mc share ls upload` 互斥。

.. mc-cmd:: upload

   *必需* 列出所有未过期的预签名上传（``PUT``）URL。

   与 :mc:`mc share ls download` 互斥。

.. mc-cmd:: ALIAS

   *必需* MinIO 部署的 :ref:`alias <alias>` 以及对象的完整路径，
   用于列出该对象的未过期预签名 URL。



全局标志
~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
--------

列出已生成的下载和上传 URL
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tab-set::

   .. tab-item:: 列出活动下载预签名 URL

      使用 :mc:`mc share ls download` 生成一个 URL，
      该 URL 支持 ``POST`` 请求，用于将文件上传到 S3 兼容主机上的
      特定对象位置：

      .. code-block:: shell
         :class: copyable

         mc share ls download ALIAS

      - 将 :mc-cmd:`ALIAS <mc share ls ALIAS>` 替换为 MinIO 部署的
        :ref:`alias <alias>`。

   .. tab-item:: 列出活动上传预签名 URL

      使用 :mc:`mc share ls upload` 生成一个 URL，
      该 URL 支持 ``POST`` 请求，用于将文件上传到 S3 兼容主机上的
      特定对象位置：

      .. code-block:: shell
         :class: copyable

         mc share ls upload ALIAS

      - 将 :mc-cmd:`ALIAS <mc share upload ALIAS>` 替换为 MinIO 部署的
        :ref:`alias <alias>`。

行为
--------

S3 兼容性
~~~~~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
