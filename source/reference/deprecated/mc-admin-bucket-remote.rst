==========================
``mc admin bucket remote``
==========================

.. default-domain:: minio

.. contents:: Table of Contents
   :local:
   :depth: 2

.. mc:: mc admin bucket remote

.. versionchanged:: RELEASE.2022-12-24T15-21-38Z

   - ``mc admin bucket remote add`` 已替换为 :mc-cmd:`mc replicate add`
   - ``mc admin bucket remote update`` 已替换为 :mc-cmd:`mc replicate update`
   - ``mc admin bucket remote rm`` 已替换为 :mc-cmd:`mc replicate rm`
   - ``mc admin bucket remote ls`` 已替换为 :mc-cmd:`mc replicate ls`

.. versionchanged:: RELEASE.2023-02-16T19-20-11Z

   - ``mc admin bucket remote bandwidth`` 已替换为 :mc-cmd:`mc replicate status`
    
     与复制相关的统计信息已迁移到 ``mc replicate status`` 命令。

描述
----

.. start-mc-admin-bucket-remote-desc

:mc-cmd:`mc admin bucket remote` 命令用于管理 ``ARN`` 资源，
供 :mc-cmd:`bucket replication <mc replicate>` 使用。

.. end-mc-admin-bucket-remote-desc

.. admonition:: 仅在 MinIO 部署上使用 ``mc admin``
   :class: note

   .. include:: /includes/facts-mc-admin.rst
      :start-after: start-minio-only
      :end-before: end-minio-only

示例
----

添加新的复制目标
~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc admin bucket remote add` 创建一个新的复制目标
ARN，供 :mc:`mc replicate add` 使用：

.. code-block:: shell
   :class: copyable

   mc admin bucket remote add SOURCE/BUCKET DESTINATION/BUCKET

- 将 :mc-cmd:`SOURCE <mc admin bucket remote add SOURCE>` 替换为
  作为复制目标的 MinIO 部署的 :mc-cmd:`alias <mc alias>`。
  将 ``BUCKET`` 替换为存储桶完整路径，MinIO 会将来自 ``DESTINATION`` 的对象复制到该存储桶。

- 将 :mc-cmd:`DESTINATION <mc admin bucket remote add DESTINATION>` 替换为
  作为复制源的 MinIO 部署的 :mc-cmd:`alias <mc alias>`。
  将 ``BUCKET`` 替换为存储桶完整路径，MinIO 会从该存储桶复制对象到 ``SOURCE``。

删除现有复制目标
~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc admin bucket remote rm` 从存储桶中删除复制目标：

.. code-block:: shell
   :class: copyable

   mc admin bucket remote rm SOURCE/BUCKET --arn ARN

- 将 :mc-cmd:`SOURCE <mc admin bucket remote rm SOURCE>` 替换为
  作为复制源使用的 MinIO 部署的 :mc-cmd:`alias <mc alias>`。
  将 ``BUCKET`` 替换为 MinIO 复制对象来源存储桶的完整路径。

- 将 :mc-cmd:`ARN <mc admin bucket remote rm ARN>` 替换为
  远程目标的 ARN。

删除该目标会停止所有正在进行的、
发往该目标的 :mc-cmd:`bucket replication <mc replicate>`。

.. _minio-retrieve-remote-bucket-targets:

查询已配置的复制目标
~~~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc replicate ls` 列出存储桶已配置的复制目标：

.. code-block:: shell
   :class: copyable

   mc replicate ls ALIAS/PATH

- 将 :mc-cmd:`ALIAS <mc replicate ls ALIAS>` 替换为
  作为复制源使用的 MinIO 部署的 :mc-cmd:`alias <mc alias>`。
  将 ``PATH`` 替换为 MinIO 复制对象来源存储桶的完整路径。

语法
----

.. mc-cmd:: add
   :fullpath:

   .. versionchanged:: RELEASE.2022-12-24T15-21-38Z

      - ``mc admin bucket remote add`` 已替换为 :mc-cmd:`mc replicate add`

   在 MinIO 部署上的存储桶中添加远程目标。该命令语法如下：

   .. code-block:: shell
      :class: copyable

      mc admin bucket remote add SOURCE DESTINATION --service "replication" [FLAGS]

   该命令接受以下参数：

   .. mc-cmd:: SOURCE

      *Required*

      要添加远程目标的存储桶完整路径。
      指定已配置 MinIO 部署的 :mc-cmd:`alias <mc alias>` 作为存储桶路径前缀。例如：

      .. code-block:: shell
         :class: copyable

         mc admin bucket remote add play/mybucket

   .. mc-cmd:: DESTINATION

      *Required*

      目标 MinIO 部署和存储桶。

      使用以下格式指定目标 MinIO 部署和存储桶的完整 URL：

      .. code-block:: shell
         :class: copyable

         http(s)://ACCESSKEY:SECRETKEY@DESTHOSTNAME/DESTBUCKET

      - 将 ``ACCESSKEY`` 替换为目标 MinIO 部署上某个用户的 access key。

      - 将 ``SECRETKEY`` 替换为目标 MinIO 部署上某个用户的 secret key。

      - 将 ``DESTHOSTNAME`` 替换为 MinIO 部署的主机名和端口
         （例如 ``minio-server.example.net:9000``）。

      - 将 ``DESTBUCKET`` 替换为目标端的存储桶。

   .. mc-cmd:: --service

      *Required*

      指定为 ``"replication"``。

   .. mc-cmd:: --region

      :mc-cmd:`~mc admin bucket remote add DESTINATION` 的地域。 

      与 :mc-cmd:`~mc admin bucket remote add` 互斥。

   .. mc-cmd:: --path

      目标服务器支持的存储桶路径查找方式。指定以下之一：

      - ``on``
      - ``off``
      - ``auto``（默认）

      与 :mc-cmd:`~mc admin bucket remote add` 互斥。

   .. mc-cmd:: --sync

      启用同步复制模式，MinIO 会在返回 PUT 对象响应*之前*尝试复制对象。
      同步复制可能会增加等待 PUT 操作成功返回的时间。

      默认情况下，:mc-cmd:`mc admin bucket remote add` 以异步模式运行，
      MinIO 会在返回 PUT 对象响应*之后*尝试复制对象。

.. mc-cmd:: ls
   :fullpath:

   .. versionchanged:: RELEASE.2022-12-24T15-21-38Z

      - ``mc admin bucket remote ls`` 已替换为 :mc-cmd:`mc replicate ls`

   列出 MinIO 部署上与某个存储桶关联的所有远程目标。
   使用 ``mc admin bucket remote ls --help`` 查看用法语法。


.. mc-cmd:: rm, remove
   :fullpath:

   .. versionchanged:: RELEASE.2022-12-24T15-21-38Z

      - ``mc admin bucket remote rm`` 已替换为 :mc-cmd:`mc replicate rm`
   
   删除 MinIO 部署上某个存储桶的远程目标。该命令语法如下：

   .. code-block:: shell
      :class: copyable

      mc admin bucket remote rm SOURCE --arn ARN

   该命令接受以下参数：

   .. mc-cmd:: SOURCE

      *Required*

      要删除远程目标的源存储桶完整路径。
      指定已配置 MinIO 部署的 :mc-cmd:`alias <mc alias>` 作为存储桶路径前缀。
      例如：

      .. code-block:: shell
         :class: copyable

         mc admin bucket remote rm play/mybucket

   .. mc-cmd:: ARN
     

      *Required*

      要从目标存储桶中删除的远程目标 ``ARN``。
      使用 :mc-cmd:`mc admin bucket remote ls` 列出指定存储桶的所有远程目标及其对应的 ARN。
