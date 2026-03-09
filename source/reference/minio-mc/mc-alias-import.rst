.. _minio-mc-alias-import:

===================
``mc alias import``
===================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc alias import

语法
----

.. start-mc-alias-import-desc

:mc:`mc alias import` 命令从 JSON 文档中导入别名配置。

.. end-mc-alias-import-desc

你可以使用 :mc:`mc alias export` 生成导入所需的 JSON。

.. tab-set::

   .. tab-item:: 示例

      以下命令从 JSON 文档中导入别名配置：

      .. code-block:: shell
         :class: copyable

         mc alias import newalias ./credentials.json

      使用 :mc:`mc alias list newalias <mc alias list>` 确认导入成功。

   .. tab-item:: 语法

      :mc:`mc alias import` 命令的语法如下：

      .. code-block:: shell

         mc [GLOBALFLAGS] alias import ALIAS PATH|STDIN

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   分配给导入配置的别名名称。

.. mc-cmd:: PATH
   :required:

   表示待导入别名配置的 JSON 对象的完整路径。

   与 :mc-cmd:`~mc alias import STDIN` 参数互斥。

.. mc-cmd:: STDIN
   :required:

   指定命令使用标准输入（STDIN）作为导入 JSON 对象的来源。

   与 :mc-cmd:`~mc alias import PATH` 参数互斥。

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

行为
----

JSON 格式
~~~~~~~~~

JSON 对象**必须**采用以下格式：

.. code-block:: json

   {
      "url" : "https://hostname:port",
      "accessKey": "<STRING>",
      "secretKey": "<STRING>",
      "api": "s3v4",
      "path": "auto"
   }

你可以使用 :mc:`mc alias export` 命令从本地主机配置中导出现有别名。
或者，你也可以从 :mc:`mc` :ref:`configuration file <mc-configuration>` 中手动提取所需的 JSON 字段。

示例
----

使用标准输入导入别名
~~~~~~~~~~~~~~~~~~~~

以下示例为 `play.min.io <https://play.min.io>`__ 沙箱导入一个自定义别名。
你可以修改该示例，改为使用你已创建或已验证存在于该沙箱上的用户凭据：

.. code-block:: shell
   :class: copyable

   echo '
   {
    "url": "https://play.min.io",
    "accessKey": "minioadmin",
    "secretKey": "minioadmin",
    "api": "s3v4",
    "path": "auto"
   }' | mc alias import play-minioadmin

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
