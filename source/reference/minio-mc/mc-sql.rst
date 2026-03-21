==========
``mc sql``
==========

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc sql

语法
----

.. start-mc-sql-desc

:mc:`mc sql` 命令提供 S3 Select 接口，用于对指定 MinIO 部署中的对象执行 SQL 查询。

.. end-mc-sql-desc

有关 S3 Select 的行为和限制，请参阅 :s3-docs:`Selecting content from objects <selecting-content-from-objects>`。

.. tab-set::

   .. tab-item:: 示例

      以下命令会查询 ``myminio`` MinIO 部署中 ``mydata`` 存储桶内的所有对象：

      .. code-block:: shell
         :class: copyable

         mc sql --recursive --query "select * from S3Object" myminio/mydata

   .. tab-item:: 语法

      该命令的语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] mc sql                          \
                          --query "string"                \
                          [--csv-input "string"]          \
                          [--compression "string"]        \
                          [--csv-output "string"]         \
                          [--csv-output-header "string"]  \
                          [--enc-c "string"]              \
                          [--json-input "string"]         \
                          [--json-output "string"]        \
                          [--recursive]                   \
                          ALIAS

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   要执行 SQL 查询的存储桶或对象的完整路径。
   指定已配置 S3 服务的 :ref:`alias <alias>` 作为 ``ALIAS`` 路径前缀。
   例如：

   .. code-block:: shell

      mc sql [FLAGS] play/mybucket

.. mc-cmd:: --query, e
   :required:

   在指定 :mc-cmd:`~mc sql ALIAS` 目录或对象上执行的 SQL 语句。
   将完整 SQL 查询用双引号 ``"`` 包裹。

   默认为 ``"select * from S3Object"``。

.. mc-cmd:: --csv-input
   :optional:

   ``.csv`` 输入对象的数据格式。
   指定由逗号分隔的 ``key=value,...`` 键值对字符串。
   有效键的更多信息请参阅 :ref:`mc-sql-csv-format`。

.. mc-cmd:: --compression
   :optional:

   输入对象的压缩类型。
   指定以下受支持值之一：

   - ``GZIP``
   - ``BZIP2``
   - ``NONE``（默认）

   仅 MinIO 后端支持以下压缩方案：

   - ``ZSTD`` `Zstandard <https://facebook.github.io/zstd/>`__
   - ``LZ4`` `LZ4 <https://lz4.github.io/lz4/>`__ 流
   - ``S2`` `S2 <https://github.com/klauspost/compress/tree/master/s2#s2-compression>`__ 帧流
   - ``SNAPPY`` `Snappy <http://google.github.io/snappy/>`__ 帧流

.. mc-cmd:: --csv-output
   :optional:

   ``.csv`` 输出的数据格式。
   指定由逗号分隔的 ``key=value,...`` 键值对字符串。
   有效键的更多信息请参阅 :ref:`mc-sql-csv-format`。

   更多信息请参阅 S3 API :s3-api:`CSVOutput <API_CSVOutput.html>`。

.. mc-cmd:: --csv-output-header
   :optional:

   ``.csv`` 输出文件的表头行。
   将逗号分隔的字段字符串指定为 ``field1,field2,...``。

   省略该参数则输出不包含表头行的 ``.csv``。

.. block include of enc-c

.. include:: /includes/common-minio-sse.rst
   :start-after: start-minio-mc-sse-c-only
   :end-before: end-minio-mc-sse-options

.. mc-cmd:: --json-input
   :optional:

   ``.json`` 或 ``.ndjson`` 输入对象的数据格式。
   将 JSON 内容类型指定为 ``type=<VALUE>``。
   值可以是：

   - ``DOCUMENT`` - JSON `document <https://www.json.org/json-en.html>`__。
   - ``LINES`` - JSON `lines <http://jsonlines.org/>`__。

   更多信息请参阅 S3 API :s3-api:`JSONInput <API_JSONInput.html>`。

.. mc-cmd:: --json-output
   :optional:

   ``.json`` 输出的数据格式。
   支持 ``rd=value`` 键，其中 ``rd`` 是 JSON 文档的 ``RecordDelimiter``。

   省略该参数则使用默认换行符 ``\n``。

   更多信息请参阅 S3 API :s3-api:`JSONOutput <API_JSONOutput.html>`。

.. mc-cmd:: --recursive, r
   :optional:

   使用 :mc-cmd:`~mc sql --query` SQL 语句递归搜索指定的 :mc-cmd:`~mc sql ALIAS` 目录。

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

选择存储桶内所有对象的所有列
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

将 :mc:`mc sql` 与 :mc-cmd:`~mc sql --recursive` 和 :mc-cmd:`~mc sql --query` 选项结合使用，可将查询应用到存储桶中的所有对象：

.. code-block:: shell
   :class: copyable

   mc sql --recursive --query "select * from S3Object" ALIAS/PATH

- 将 :mc-cmd:`ALIAS <mc sql ALIAS>` 替换为 MinIO 部署的 :ref:`alias <alias>`。

- 将 :mc-cmd:`PATH <mc sql ALIAS>` 替换为 MinIO 部署上存储桶的路径。

在对象上运行聚合查询
~~~~~~~~~~~~~~~~~~~~

将 :mc:`mc sql` 与 :mc-cmd:`~mc sql --query` 选项结合使用，可查询 MinIO 部署上的对象：

.. code-block:: shell

   mc sql --query "select count(s.power) from S3Object" ALIAS/PATH

- 将 :mc-cmd:`ALIAS <mc sql ALIAS>` 替换为 MinIO 部署的 :ref:`alias <alias>`。

- 将 :mc-cmd:`PATH <mc sql ALIAS>` 替换为 MinIO 部署上对象的路径。

行为
----

输入格式
~~~~~~~~

:mc:`mc sql` 支持以下输入格式：

.. list-table:: 输入格式类型
   :header-rows: 1
		 
   * - 类型
     - ``content-type`` 值

   * - ``.csv``
     - ``text/csv``

   * - ``.json``
     - ``application/json``

   * - ``.parquet``
     - 无

对于 ``.csv`` 文件类型，使用 :mc-cmd:`mc sql --csv-input` 指定 CSV 数据格式。
有关 CSV 格式字段的更多信息，请参阅 :ref:`mc-sql-csv-format`。

对于 ``.json`` 文件类型，使用 :mc-cmd:`mc sql --json-input` 指定 JSON 数据格式。

对于 ``.parquet`` 文件类型，:mc:`mc sql` 会自动解析数据格式。

:mc:`mc sql` 通过目标对象的文件扩展名判断类型。
例如，名为 ``data.json`` 的对象会被解释为 JSON 文件。

如果对象具有合适的 ``content-type``，你也可以查询受支持类型但扩展名不同的数据。
更多信息请参阅 :mc-cmd:`mc cp --attr`。

.. _mc-sql-csv-format:

CSV 格式字段
~~~~~~~~~~~~

下表列出了可用于 :mc-cmd:`mc sql --csv-input` 和 :mc-cmd:`mc sql --csv-output` 的有效键值对。
某些键值对仅适用于 :mc-cmd:`~mc sql --csv-input`。
有关 S3 CSV 格式的更多信息，请参阅 S3 API :s3-api:`CSVInput <API_CSVInput.html>` 文档。

.. list-table::
   :header-rows: 1
   :widths: 20 20 60
   :width: 100%

   * - 键
     - 仅 ``--csv-input``
     - 说明

   * - ``rd``
     -
     - 用于分隔输入 ``.csv`` 文件中每条记录（行）的字符。

       对应 S3 API ``CSVInput`` 中的 ``RecordDelimiter``。

   * - ``fd``
     -
     - 用于分隔一条记录中各字段的字符。默认为 ``,``。

       对应 S3 API ``CSVInput`` 中的 ``FieldDelimeter``。

   * - ``qc``
     -
     - 当 ``fd`` 字符是字段值的一部分时，用于转义的字符。默认为 ``"``。

       对应 S3 API ``CSVInput`` 中的 ``QuoteCharacter``。

   * - ``qec``
     -
     - 在已转义值内部，用于转义引号 ``"`` 字符的字符。

       对应 S3 API ``CSVInput`` 中的 ``QuoteEscapeCharacter``。

   * - ``fh``
     - 是
     - ``.csv`` 文件第一行的内容类型。

       指定以下受支持值之一：

       - ``NONE`` - 第一行不是表头。
       - ``IGNORE`` - 忽略第一行。
       - ``USE`` - 第一行是表头。

       对于 ``NONE`` 或 ``IGNORE``，你必须在 :mc-cmd:`~mc sql --query` 语句中指定列位置 ``_#`` 来标识列。

       对于 ``USE``，你可以在 :mc-cmd:`~mc sql --query` 语句中指定表头值来标识列。

       对应 S3 API ``CSVInput`` 中的 ``FieldHeaderInfo``。

   * - ``cc``
     - 是
     - 用于指示应忽略某条记录的字符。
       该字符 *必须* 出现在记录开头。

       对应 S3 API ``CSVInput`` 中的 ``Comment``。

   * - ``qrd``
     - 是
     - 指定 ``TRUE`` 表示字段中可以包含记录分隔符值（``rd``）。

       默认为 ``FALSE``。

       对应 S3 API ``CSVInput`` 中的 ``AllowQuotedRecordDelimiter``。

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
