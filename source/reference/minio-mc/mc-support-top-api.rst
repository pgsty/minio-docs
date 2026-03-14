======================
``mc support top api``
======================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc support top api

.. include:: /includes/common-mc-support.rst
   :start-after: start-minio-only
   :end-before: end-minio-only

语法
----

.. start-mc-support-top-api-desc

:mc:`mc support top api` 命令用于汇总 MinIO 部署服务器上的实时 API 事件。

.. end-mc-support-top-api-desc

.. tab-set::

   .. tab-item:: 示例

      以下命令显示 :term:`alias` ``myminio`` 上当前正在进行的 S3 API 调用。

      .. code-block:: shell
         :class: copyable

         mc support top api myminio/

   .. tab-item:: 语法

      该命令的语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] support top api    \
                          TARGET             \
                          [--name "string"]  \
                          [--path "string"]  \
                          [--node "string"]  \
                          [--errors, -e]

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~

.. mc-cmd:: TARGET
   :required:

   命令应在其上运行的 alias、前缀或对象完整路径。
   该路径至少必须包含一个 :ref:`ALIAS <minio-mc-alias>`。

.. mc-cmd:: --name
   :optional:

   输出与输入字符串匹配的当前 API 调用摘要。


.. mc-cmd:: --path
   :optional:

   输出指定路径的当前 API 调用摘要。

.. mc-cmd:: --node
   :optional:

   输出匹配服务器上的当前 API 调用摘要。

.. mc-cmd:: --errors, -e
   :optional:

   输出返回错误的当前 API 调用摘要。

全局参数
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

显示当前所有正在进行的 S3 API 调用
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下命令显示 ``myminio`` 部署中所有正在进行的 S3 API 调用：

.. code-block:: shell
   :class: copyable

   mc support top api myminio/

显示当前正在进行的 ``s3.PutObject`` 调用
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下命令显示 ``myminio`` 部署中所有正在进行的 ``s3.PutObject`` 调用：

.. code-block:: shell
   :class: copyable

   mc support top api --name s3.PutObject myminio/
