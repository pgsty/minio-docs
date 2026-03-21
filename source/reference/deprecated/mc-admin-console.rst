====================
``mc admin console``
====================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc admin console

.. important::

   此命令已在 `mc RELEASE.2022-12-02T23-48-47Z <https://github.com/minio/mc/releases/tag/RELEASE.2022-12-02T23-48-47Z>`__ 中被 :mc:`mc admin logs` 替代。

   该命令此前已在 `mc RELEASE.2022-06-26T18-51-48Z <https://github.com/minio/mc/tree/RELEASE.2022-06-26T18-51-48Z>`__ 中被 ``mc support logs show`` 替代。

描述
----

:mc:`mc admin console` 命令返回部署中每个 MinIO 服务器的服务端日志条目。

.. admonition:: 仅在 MinIO 部署上使用 ``mc admin``
   :class: note

   .. include:: /includes/facts-mc-admin.rst
      :start-after: start-minio-only
      :end-before: end-minio-only


语法
----

:mc:`mc admin console` 的语法如下：

.. code-block:: shell
   :class: copyable

   mc admin console [FLAGS] TARGET NODENAME

:mc:`mc admin console` 支持以下项：

.. mc-cmd:: TARGET

   已配置 MinIO 部署的 :mc:`alias <mc alias>`，命令将从该部署中检索服务端日志。

.. mc-cmd:: NODENAME

   命令从中检索服务端日志的特定 MinIO 服务器节点。

.. mc-cmd:: --limit, l
   

   要显示的最新日志条目数量。默认为 ``10``。

.. mc-cmd:: --type, t
   

   要返回的错误日志类型。以逗号分隔的 ``,`` 列表形式指定以下一个或多个选项：

   - ``minio``
   - ``application``
   - ``all``（默认）
