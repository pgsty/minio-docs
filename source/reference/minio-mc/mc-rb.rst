=========
``mc rb``
=========

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc rb

语法
----

.. start-mc-rb-desc

:mc:`mc rb` 命令用于删除 MinIO *或* 其他兼容 S3 服务上的一个或多个存储桶。

如仅需删除存储桶内容，请改用 :mc:`mc rm`。

.. end-mc-rb-desc

.. important::

   :mc:`mc rb` 会在目标部署上*永久删除存储桶*，
   包括所有 :ref:`对象版本 <minio-bucket-versioning>`
   以及存储桶配置，例如
   :ref:`生命周期管理 <minio-lifecycle-management>` 或
   :ref:`复制 <minio-bucket-replication-serverside>`。

你也可以对本地文件系统使用 :mc:`mc rb`，其效果与 ``rm --rf`` 命令行工具类似。

.. tab-set::

   .. tab-item:: EXAMPLE

      以下命令删除 ``myminio`` MinIO 部署上的 ``mydata`` 存储桶：

      .. code-block:: shell
         :class: copyable

         mc rb --force myminio/mydata

   .. tab-item:: SYNTAX

      命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] rb             \
                          --force        \
                          [--dangerous]  \
                          ALIAS [ALIAS...]

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


参数
~~~~

.. mc-cmd:: ALIAS

   *必需* MinIO 或其他兼容 S3 服务的 :ref:`alias <alias>`，以及要删除的存储桶完整路径。例如：

   .. code-block:: none

      mc rb --force myminio/mydata

   省略存储桶路径可对 MinIO 部署执行站点范围的存储桶删除操作。
   此操作*必须*指定 :mc-cmd:`~mc rb --dangerous`，以明确确认会永久删除该部署上的*全部*数据。例如：

   .. code-block:: none

      mc rb --force --dangerous myminio

   若要删除本地文件系统上的目录及其内容，请指定该目录的完整路径。
   如果指定了 :mc-cmd:`~mc rb --force` 标志，也会被忽略。例如：

   .. code-block:: none

      mc rb ~/data/myolddata

   你可以指定多个 ``ALIAS`` 目标，目标可以是 MinIO 或本地文件系统目录。
   命令会尝试删除*所有*指定目标。例如：

   .. code-block:: none

      mc rb --force myminio/mydata ~/data/myolddata

.. mc-cmd:: --force
   

   *必需* 用于确认删除存储桶内容的安全标志。

.. mc-cmd:: --dangerous
   

   *可选* 指示 :mc:`mc rb` 在每个指定的 :mc-cmd:`~mc rb ALIAS`（例如 ``myminio/``）上执行站点范围的全部存储桶删除。

   如果任一 ``ALIAS`` 指定的是文件系统目录，此选项会删除该目录路径下的所有子目录和文件，效果类似 ``rm --rf``。

   .. warning::

      执行 :mc-cmd:`mc rb --dangerous` 不可逆。
      在执行前请尽可能审慎核对，确保命令仅作用于预期的 ``ALIAS`` 目标。

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

删除存储桶
~~~~~~~~~~

.. code-block:: shell
   :class: copyable

   mc rb --force ALIAS/PATH

- 将 :mc-cmd:`ALIAS <mc rb ALIAS>` 替换为已配置的兼容 S3 主机的 :mc-cmd:`alias <mc alias>`。

- 将 :mc-cmd:`PATH <mc rb ALIAS>` 替换为要删除的存储桶路径。

行为
----

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
