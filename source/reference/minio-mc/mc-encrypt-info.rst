.. _minio-mc-encrypt-info:

===================
``mc encrypt info``
===================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc encrypt info

语法
----

.. start-mc-encrypt-info-desc

:mc:`mc encrypt info` 命令返回存储桶当前的默认加密设置。

.. end-mc-encrypt-info-desc

.. tab-set::

   .. tab-item:: 示例

      以下命令返回 ``myminio`` MinIO 部署上 ``mydata`` 存储桶的默认加密设置。

      .. code-block:: shell
         :class: copyable

         mc encrypt info myminio/mydata

   .. tab-item:: 语法

      该命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] encrypt info ALIAS

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~

.. mc-cmd:: ALIAS

   要检索其默认 SSE 模式的存储桶完整路径。
   指定 MinIO 部署的 :ref:`alias <alias>` 作为 ALIAS 路径前缀。例如：

   .. code-block:: shell

      mc encrypt info play/mybucket

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

获取存储桶的自动服务端加密设置
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tab-set::

   .. tab-item:: 示例

      .. code-block:: shell
         :class: copyable

          mc encrypt info myminio/data

   .. tab-item:: 语法

      .. code-block:: shell
         :class: copyable

         mc encrypt info ALIAS

      - 将 ``ALIAS`` 替换为要为其配置存储桶自动服务端加密的 MinIO 部署
        :ref:`alias <alias>`。

行为
----

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
