=========
``mc ls``
=========

.. default-domain:: minio

.. contents:: Table of Contents
   :local:
   :depth: 2

.. mc:: mc ls

.. Replacement substitutions

.. |command| replace:: :mc:`mc ls`
.. |rewind| replace:: :mc-cmd:`~mc ls --rewind`
.. |versions| replace:: :mc-cmd:`~mc ls --versions`
.. |alias| replace:: :mc-cmd:`~mc ls ALIAS`

语法
------

.. start-mc-ls-desc

:mc:`mc ls` 命令用于列出 MinIO 或其他 S3 兼容服务上的存储桶和对象。

.. end-mc-ls-desc

你也可以对本地文件系统使用 :mc:`mc ls`，生成与 ``ls`` 命令类似的结果。

.. tab-set::

   .. tab-item:: 示例

      以下命令列出 ``myminio`` MinIO 部署中 ``mydata`` 存储桶内的所有对象*及*对象版本：

      .. code-block:: shell
         :class: copyable

         mc ls --recursive --versions myminio/mydata

      输出类似如下：

      .. code-block:: shell

         [2022-11-08 11:30:24 PST]    52MB  STANDARD log-data.csv
         [2022-11-09 12:20:18 PST]    120MB WARM videos/event-2022-11-09.mp4

      - ``STANDARD`` 表示存储在 MinIO 部署上的对象
      - ``WARM`` 表示存储在同名远端层中的对象
      - ``videos/`` 表示对象的前缀

   .. tab-item:: 语法

      该命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] ls              \
                          [--incomplete]  \
                          [--recursive]   \
                          [--rewind]      \
                          [--versions]    \
                          [--summarize]   \
                          ALIAS [ALIAS ...]

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


参数
~~~~~~~~~~

.. mc-cmd:: ALIAS

   *必需* 要复制的一个或多个对象。

   对于列出 MinIO 上的对象，
   指定该对象的 :ref:`alias <alias>` 和完整路径
   （例如存储桶和对象路径）。例如：

   .. code-block:: none

      mc ls play/mybucket/object.txt


   对于列出本地文件系统上的对象，指定该对象的完整路径。
   例如：

   .. code-block:: none

      mc ls ~/mydata/object.txt
   
   如果你在 :mc-cmd:`~mc ls ALIAS` 中指定的是目录或存储桶，则还必须
   指定 :mc-cmd:`~mc ls --recursive`，以递归列出该目录或存储桶的内容。
   如果省略 ``--recursive`` 参数，:mc:`~mc ls` 仅列出指定目录或存储桶
   顶层的对象。


.. mc-cmd:: incomplete, -I
   

   *可选* 返回指定 :mc-cmd:`~mc ls ALIAS` 存储桶上的所有未完成上传。

.. mc-cmd:: --recursive, r
   

   *可选* 递归列出 :mc-cmd:`~mc ls ALIAS` 中各存储桶或目录的内容。

.. mc-cmd:: --rewind
   :optional:
   
   .. include:: /includes/facts-versioning.rst
      :start-after: start-rewind-desc
      :end-before: end-rewind-desc

   同时使用 :mc-cmd:`~mc ls --rewind` 和
   :mc-cmd:`~mc ls --versions`，可显示在特定时间点存在的对象版本。

.. mc-cmd:: --versions
   :optional:   

   .. include:: /includes/facts-versioning.rst
      :start-after: start-versions-desc
      :end-before: end-versions-desc

   同时使用 :mc-cmd:`~mc ls --versions` 和
   :mc-cmd:`~mc ls --rewind`，可显示在特定时间点存在的对象版本。

.. mc-cmd:: --summarize
   

   *可选* 显示指定 ``ALIAS`` 路径的汇总信息。

全局标志
~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
--------

列出存储桶内容
~~~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc ls <mc ls ALIAS>` 列出存储桶内容：

.. code-block:: shell
   :class: copyable

   mc ls [--recursive] ALIAS/PATH

- 将 :mc-cmd:`ALIAS <mc ls ALIAS>` 替换为
  S3 兼容主机的 :mc:`alias <mc alias>`。

- 将 :mc-cmd:`PATH <mc ls ALIAS>` 替换为 S3 兼容主机上
  存储桶的路径。

  如果指定的是 S3 根路径（仅 ``ALIAS``），请包含
  :mc-cmd:`~mc ls --recursive` 选项。

列出对象版本
~~~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc ls --versions` 列出对象的所有版本：

.. code-block:: shell
   :class: copyable

   mc ls --versions ALIAS/PATH

- 将 :mc-cmd:`ALIAS <mc ls ALIAS>` 替换为
  S3 兼容主机的 :mc:`alias <mc alias>`。

- 将 :mc-cmd:`PATH <mc ls ALIAS>` 替换为 S3 兼容主机上
  存储桶或对象的路径。

.. include:: /includes/facts-versioning.rst
   :start-after: start-versioning-admonition
   :end-before: end-versioning-admonition

列出某个时间点的存储桶内容
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc ls --versions` 列出对象的所有版本：

.. code-block:: shell
   :class: copyable

   mc ls --rewind DURATION ALIAS/PATH

- 将 :mc-cmd:`ALIAS <mc ls ALIAS>` 替换为
  S3 兼容主机的 :mc:`alias <mc alias>`。

- 将 :mc-cmd:`PATH <mc ls ALIAS>` 替换为 S3 兼容主机上
  存储桶或对象的路径。

- 将 :mc-cmd:`DURATION <mc ls --rewind>` 替换为过去的某个时间点，
  命令将在该时间点返回对象。例如，指定 ``30d`` 以返回相对于当前日期
  往前 30 天的对象版本。

.. include:: /includes/facts-versioning.rst
   :start-after: start-versioning-admonition
   :end-before: end-versioning-admonition

行为
--------

S3 兼容性
~~~~~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
