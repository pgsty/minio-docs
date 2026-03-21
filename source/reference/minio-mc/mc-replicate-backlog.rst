.. _minio-mc-replicate-diff:
.. _minio-mc-replicate-backlog:

========================
``mc replicate backlog``
========================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc replicate diff
.. mc:: mc replicate backlog

.. versionchanged:: mc.RELEASE.2023-07-18T21-05-38Z

   ``mc replicate diff`` 已重命名为 ``mc replicate backlog``。
   功能未发生变化。

描述
----

.. start-mc-replicate-backlog-desc

:mc:`mc replicate backlog` 显示尚未复制的新建或已删除对象列表。

.. end-mc-replicate-backlog-desc

你可以列出特定远程目标的对象复制状态。
为此，你必须具有该远程目标的 ARN。
你可以使用 :ref:`检索为存储桶配置的远程目标 <minio-retrieve-remote-bucket-targets>` 查找 ARN。

语法
----

.. tab-set::

   .. tab-item:: 示例

      以下命令显示 ``myminio`` 别名下 ``notes`` 存储桶中 ``teamorange/projects`` 前缀里尚未复制到特定远程目标存储桶的新建或已删除对象。
      该远程目标的 ARN 为 ``arn:minio:replication::3bb8c736-4014-42c5-b3cb-d64e3ebaa75e:notes``。

      .. code-block:: shell
         :class: copyable

         mc replicate backlog myminio/notes/teamorange/projects --arn arn:minio:replication::3bb8c736-4014-42c5-b3cb-d64e3ebaa75e:notes

      如果存在尚未复制的新建或已删除对象，命令输出类似如下：

      .. code-block:: shell

         [0001-01-01 00:00:00 UTC] [2022-10-06 17:18:59 UTC]          478efe49-aa9d-46ab-8268-45b70cc4c341 PUT agenda.docx
         [0001-01-01 00:00:00 UTC] [2022-10-06 17:18:15 UTC]          b283bf43-319f-455a-a779-3c2e669fad88 PUT budget-meeting.docx

      在输出中，``PUT`` 对应新建对象。
      已删除对象或版本会显示为 ``DEL``。

   .. tab-item:: 语法

      命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] replicate backlog   \
                          [--arn "string"]    \
                          TARGET

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~

.. mc-cmd:: TARGET
   :required:

   指向别名、前缀或对象的路径。

.. mc-cmd:: arn
   :optional:

   远程存储桶的 ARN，用于检查尚未复制的新建或已删除对象。

   指定后，该命令返回尚未复制到远程目标的所有新建或已删除对象列表。
   未指定时，该命令返回源部署上尚未复制到任何远程目标的新建或已删除对象列表。

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

查看某个前缀下对象的未复制版本
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

显示某个前缀下未复制的 ``PUT`` 和 ``DELETE`` 操作：

.. code-block:: shell
   :class: copyable

   mc replicate backlog myminio/mybucket/path/to/prefix

- 将 ``myminio/mybucket`` 替换为 :mc-cmd:`~mc replicate add ALIAS` 以及
  需要创建复制配置的完整存储桶路径。

- 将 ``path/to/prefix`` 替换为请求要使用的前缀或对象。

如果存在未复制对象，输出会返回一个操作列表，列出在该前缀下创建或删除对象且尚未复制到远程目标的操作：

.. code-block:: shell

   [0001-01-01 00:00:00 UTC] [2022-10-06 17:18:59 UTC]          478efe49-aa9d-46ab-8268-45b70cc4c341 PUT agenda.docx
   [0001-01-01 00:00:00 UTC] [2022-10-06 17:18:15 UTC]          b283bf43-319f-455a-a779-3c2e669fad88 PUT budget-meeting.docx

查看特定远程目标上的未复制对象
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下 :mc:`mc replicate backlog` 命令显示特定远程目标在某个 alias/bucket/prefix 路径上的未复制对象：

.. code-block:: shell
   :class: copyable

   mc replicate backlog myminio/mybucket/path/to/prefix --arn <remote-arn>

- 将 ``myminio/mybucket`` 替换为 :mc-cmd:`~mc replicate add ALIAS` 以及
  需要显示未复制对象的完整存储桶路径。

- 将 ``path/to/prefix`` 替换为所需的前缀或对象路径。

- 将 ``<remote-arn>`` 替换为特定远程目标的资源编号。

如果存在未复制对象，输出会返回一个操作列表，列出创建或删除对象且尚未复制到远程目标的操作：

.. code-block:: shell

   [0001-01-01 00:00:00 UTC] [2022-10-06 17:18:59 UTC]          478efe49-aa9d-46ab-8268-45b70cc4c341 PUT agenda.docx
   [0001-01-01 00:00:00 UTC] [2022-10-06 17:18:15 UTC]          b283bf43-319f-455a-a779-3c2e669fad88 PUT budget-meeting.docx

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
