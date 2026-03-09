.. _minio-mc-find:

===========
``mc find``
===========

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc find

语法
----

.. start-mc-find-desc

:mc:`mc find` 命令支持在 MinIO 部署上搜索对象。
你也可以使用该命令在文件系统上搜索文件。

.. end-mc-find-desc

.. tab-set::

   .. tab-item:: EXAMPLE

      以下命令会在 ``myminio`` MinIO 部署的 ``mydata`` 存储桶中，
      搜索所有匹配指定模式的对象：

      .. code-block:: shell
         :class: copyable

         mc find myminio/mydata --name "*.jpg"

   .. tab-item:: SYNTAX

      该命令的语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] find                    \
                          [--exec "string"]       \
                          [--ignore "string"]     \
                          [--larger "string"]     \
                          [--maxdepth "string"]   \
                          [--metadata "string"]   \
                          [--name "string"]       \
                          [--newer-than "string"] \
                          [--older-than "string"] \
                          [--path "string"]       \
                          [--print "string"]      \
                          [--regex "string"]      \
                          [--smaller "string"]    \
                          [--tags "string"]`      \
                          [--versions]            \
                          [--watch]               \
                          ALIAS

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   对于 MinIO 或 S3 兼容主机上的对象，指定 :ref:`alias <alias>` 和完整搜索路径（例如存储桶与前缀）。
   例如：

   .. code-block:: none

      mc find play/mydata/

   对于文件系统上的对象，指定要搜索的完整路径。
   例如：

   .. code-block:: shell

      mc find ~/mydata/

   执行 :mc-cmd:`mc find ALIAS` 且不带其他参数时，会返回指定路径下
   *所有* 对象或文件的列表，行为类似 :mc:`mc ls`。

.. mc-cmd:: --exec
   :optional:

   对 :mc:`mc find` 返回的每个对象启动一个外部进程。
   支持对输出进行 :ref:`替换格式化 <mc-find-substitution-format>`。

.. mc-cmd:: --ignore
   :optional:

   排除名称匹配指定 :ref:`通配符模式 <minio-wildcard-matching>` 的对象。

.. mc-cmd:: --larger
   :optional:

   匹配所有大于指定大小的对象，大小单位见 :ref:`units <mc-find-units>`。

.. mc-cmd:: --maxdepth
   :optional:

   将目录遍历限制为指定深度。

.. mc-cmd:: --metadata
   :optional:

   .. versionadded:: mc RELEASE.2023-04-12T02-21-51Z

   **仅用于 MinIO 部署。**

   返回元数据匹配指定 ``key=value`` 的对象。
   使用格式 ``--metadata="KEY=value"``。

   你可以传入值为空的 key。
   在这种情况下，``mc find`` 会匹配不包含该元数据 key 的对象，或该元数据 key 的值为空的对象。

   你可以多次使用该选项，以匹配更多元数据 key。
   要返回对象，该对象必须在所有元数据 key 上都匹配。

.. mc-cmd:: --name
   :optional:

   返回名称匹配指定 :ref:`通配符模式 <minio-wildcard-matching>` 的对象。

.. mc-cmd:: --newer-than
   :optional:

   匹配晚于指定天数的对象。
   指定 ``#d#hh#mm#ss`` 格式的字符串。
   例如：``--older-than 1d2hh3mm4ss``

   .. versionchanged:: RELEASE.2025-02-04T04-57-50Z
      日期时间也可以使用 ``YYYY-MM-DD HH:MM:SS TMZ`` 格式的绝对时间指定。
      例如，``mc find --newer-than="2025-01-22 09:57:00 CET" minioalias/mybucket``。

.. mc-cmd:: --older-than
   :optional:

   匹配早于指定时间限制的对象。指定
   ``#d#hh#mm#ss`` 格式的字符串。
   例如：``--older-than 1d2hh3mm4ss``

   .. versionchanged:: RELEASE.2025-02-04T04-57-50Z
      日期时间也可以使用 ``YYYY-MM-DD HH:MM:SS TMZ`` 格式的绝对时间指定。
      例如，``mc find --newer-than="2025-01-22 09:57:00 CET" minioalias/mybucket``。

   默认为 ``0``（所有对象）。

.. mc-cmd:: --path
   :optional:

   返回名称匹配指定 :ref:`通配符模式 <minio-wildcard-matching>` 的目录内容。

.. mc-cmd:: --print
   :optional:

   将结果打印到 ``STDOUT``。
   支持对输出进行 :ref:`替换格式化 <mc-find-substitution-format>`。

.. mc-cmd:: --regex
   :optional:

   返回名称匹配指定 PCRE 正则表达式模式的对象
   或目录内容。

.. mc-cmd:: --tags
   :optional:

   .. versionadded:: mc RELEASE.2023-04-12T02-21-51Z

   **仅用于 MinIO 部署。**

   返回标签匹配指定 `RE2 RegEx pattern <https://github.com/google/re2/wiki/Syntax>`__ 的对象。
   使用格式 ``--tag="KEY=regexValue"``。

   你可以传入值为空的 key。
   在这种情况下，``mc find`` 会匹配不包含该元数据 key 的对象，或该元数据 key 的值为空的对象。

   你可以多次使用该选项，以匹配更多标签。
   要返回对象，该对象必须在所有标签上都匹配。

.. mc-cmd:: --smaller
   :optional:

   匹配所有小于指定大小的对象，
   大小单位见 :ref:`units <mc-find-units>`。

.. mc-cmd:: --versions
   :optional:

   在结果中包含对象的所有版本。

.. mc-cmd:: --watch
   :optional:

   持续监控 :mc-cmd:`~mc find ALIAS`，并返回任何
   匹配指定条件的新对象。

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

在存储桶中查找特定对象
~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: shell
   :class: copyable

   mc find ALIAS/PATH --name NAME

- 将 :mc-cmd:`ALIAS <mc find ALIAS>` 替换为
  S3 兼容主机的 :mc:`alias <mc alias>`。

- 将 :mc-cmd:`PATH <mc find ALIAS>` 替换为 S3 兼容主机上的存储桶路径。
  省略该路径可从 S3 主机根路径开始搜索。

- 将 :mc-cmd:`NAME <mc find --name>` 替换为对象。

在存储桶中按文件扩展名查找对象
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: shell
   :class: copyable

   mc find ALIAS/PATH --name *.EXTENSION

- 将 :mc-cmd:`ALIAS <mc find ALIAS>` 替换为
  S3 兼容主机的 :mc:`alias <mc alias>`。

- 将 :mc-cmd:`PATH <mc find ALIAS>` 替换为 S3 兼容主机上的存储桶路径。

- 将 :mc-cmd:`EXTENSION <mc find --name>` 替换为对象的文件扩展名。

查找所有匹配文件并复制到 S3 服务
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

将 :mc:`mc find` 与 :mc-cmd:`~mc find --exec` 选项结合使用，可在本地文件系统中查找
文件，并将其传递给 :program:`mc` 命令进行进一步处理。以下示例使用 :mc:`mc cp` 将
:mc:`mc find` 的输出复制到 S3 兼容主机。

.. code-block:: shell
   :class: copyable

   mc find FILEPATH --name "*.EXTENSION" --exec "mc cp {} ALIAS/PATH"

- 将 :mc-cmd:`FILEPATH <mc find ALIAS>` 替换为要搜索目录的
  完整文件路径。

- 将 :mc-cmd:`EXTENSION <mc find --name>` 替换为对象的文件扩展名。

- 将 :mc-cmd:`ALIAS <mc find ALIAS>` 替换为
  S3 兼容主机的 :mc:`alias <mc alias>`。

- 将 :mc-cmd:`PATH <mc find ALIAS>` 替换为 S3 兼容主机上的存储桶路径。

如需持续监视指定目录并复制新对象，
请添加 :mc-cmd:`~mc find --watch` 参数：

.. code-block:: shell
   :class: copyable

   mc find --watch FILEPATH --name "*.EXTENSION" --exec "mc cp {} ALIAS/PATH"

查找具有匹配标签的对象
~~~~~~~~~~~~~~~~~~~~~~

.. note::

   标签匹配仅适用于 MinIO 部署。

.. code-block:: shell
   :class: copyable

   mc find --tags="key=v*" ALIAS/BUCKET/

- 将 ``key`` 替换为要匹配的标签键名。

- 将 ``v*`` 替换为要用于匹配的 RE2 正则表达式。

- 将 ``ALIAS`` 替换为 MinIO 部署的 :mc:`alias <mc alias>`。

- 将 ``BUCKET`` 替换为要搜索的存储桶或前缀。

你可以添加更多 ``--tags="key=RegExpression"`` 标志进行匹配。
匹配对象必须满足所有包含的标签条件。

查找具有匹配元数据的对象
~~~~~~~~~~~~~~~~~~~~~~~~

.. note::

   元数据匹配仅适用于 MinIO 部署。

.. code-block:: shell
   :class: copyable

   mc find --json --metadata="content-type=text/csv" ALIAS/BUCKET/

- 将 ``content-type=text/csv`` 替换为要匹配的元数据字段和值的键值对。

- 将 ``ALIAS`` 替换为 MinIO 部署的 :mc:`alias <mc alias>`。

- 将 ``BUCKET`` 替换为要搜索的存储桶或前缀。

你可以添加更多 ``--tags="metadata=value"`` 标志进行匹配。
匹配对象必须满足所有包含的元数据字段条件。

行为
----

.. _mc-find-units:

计量单位
~~~~~~~~

:mc-cmd:`mc find --smaller` 和 :mc-cmd:`mc find --larger` 标志
接受以下不区分大小写的后缀，用于表示指定大小值的单位：

.. list-table::
   :header-rows: 1
   :widths: 20 80
   :width: 100%

   * - 后缀
     - 单位大小

   * - ``k``
     - KB（千字节，1000 字节）

   * - ``m``
     - MB（兆字节，1000 千字节）

   * - ``g``
     - GB（吉字节，1000 兆字节）

   * - ``t``
     - TB（太字节，1000 吉字节）

   * - ``ki``
     - KiB（Kibibyte，1024 字节）

   * - ``mi``
     - MiB（Mebibyte，1024 Kibibytes）

   * - ``gi``
     - GiB（Gibibyte，1024 Mebibytes）

   * - ``ti``
     - TiB（Tebibyte，1024 Gibibytes）

省略后缀时默认单位为 ``bytes``。


.. _mc-find-substitution-format:

替换格式
~~~~~~~~

:mc-cmd:`mc find --exec` 和 :mc-cmd:`mc find --print` 命令
支持字符串替换，对以下关键字具有特殊解释。

以下关键字同时适用于文件系统和 S3 服务目标：

- ``{}`` - 替换为完整路径。
- ``{base}`` - 替换为路径的 basename。
- ``{dir}`` - 替换为路径的 dirname。
- ``{size}`` - 替换为路径对应对象的大小。
- ``{time}`` - 替换为路径对应对象的修改时间。

以下关键字仅适用于 S3 服务目标：

- ``{url}`` - 替换为路径的可共享 URL。

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
