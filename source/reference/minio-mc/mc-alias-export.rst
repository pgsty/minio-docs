.. _minio-mc-alias-export:

===================
``mc alias export``
===================

.. default-domain:: minio

.. contents:: Table of Contents
   :local:
   :depth: 2

.. mc:: mc alias export

.. versionadded:: mc.RELEASE.2023-11-15T22-45-58Z

语法
----

.. start-mc-alias-export-desc

:mc:`mc alias export` 命令从现有的 :ref:`configuration <mc-configuration>` 中导出别名配置。

.. end-mc-alias-export-desc

该命令将结果输出到 ``STDOUT``，你可以将输出保存为文件，*或* 按需进一步修改输出内容。

使用 :mc:`mc alias import` 命令导入生成的 JSON 配置。

.. tab-set::

   .. tab-item:: 示例

      以下命令从现有主机导出别名配置并输出到文件：

      .. code-block:: shell
         :class: copyable

         mc alias export play > play.json

      该命令会将内容输出到标准输出（``STDOUT``）。
      你也可以将输出通过管道传递给所选工具执行后续操作。


   .. tab-item:: 语法

      :mc:`mc alias export` 命令的语法如下：

      .. code-block:: shell

         mc [GLOBALFLAGS] alias export ALIAS

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   要导出的别名名称。

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

行为
----

JSON 格式
~~~~~~~~~

该命令输出一个符合以下结构的 JSON 对象：

.. code-block:: json

   {
      "url" : "https://hostname:port",
      "accessKey": "<STRING>",
      "secretKey": "<STRING>",
      "api": "s3v4",
      "path": "auto"
   }

你可以使用 :mc:`mc alias import` 导入该 JSON 文档。

示例
----

导出并转换别名
~~~~~~~~~~~~~~

以下示例导出 `play.min.io <https://play.min.io>`__ 沙箱的别名。
随后使用 `jq <https://jqlang.github.io/jq/>`__ 工具转换该配置，并基于修改后的配置创建新别名：

.. code-block:: shell
   :class: copyable

   mc alias export play | jq '.accessKey = "minioadmin" | .secretKey = "minioadmin"' | mc alias import play-custom

备份别名配置
~~~~~~~~~~~~

以下命令将别名配置导出为 JSON 文件。
然后你可以按你偏好的流程备份该文件。

.. code-block:: shell
   :class: copyable

   mc alias export play > play-backup.json

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
