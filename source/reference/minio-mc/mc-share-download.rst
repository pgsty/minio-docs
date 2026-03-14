=====================
``mc share download``
=====================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc share download

.. |command| replace:: :mc:`mc share download`
.. |versionid| replace:: :mc-cmd:`~mc share download --version-id`
.. |alias| replace:: :mc-cmd:`~mc share download ALIAS`

语法
----

.. start-mc-share-download-desc

:mc:`mc share download` 命令会生成一个临时的预签名 URL，并集成访问凭证，
用于从 MinIO 存储桶下载对象。该临时 URL 会在可配置的时间限制后过期。

.. end-mc-share-download-desc

- 应用程序可以通过执行 ``GET`` 从该 URL 获取对象。
- 用户可以在浏览器中打开该 URL 下载对象。

有关可共享对象 URL 的更多信息，请参阅 Amazon S3 关于 :aws-docs:`Pre-Signed URLs
<AmazonS3/latest/dev/ShareObjectPreSignedURL.html>`.

.. tab-set::

   .. tab-item:: 示例

      以下命令会为 ``myminio`` MinIO 部署中的 ``mydata`` 存储桶生成一个新的预签名下载 URL：

      .. code-block:: shell
         :class: copyable

         mc share download --recursive myminio/mydata

   .. tab-item:: 语法

      该命令的语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] share download           \
                          [--expire "string"]      \
                          [--recursive]            \
                          [--version-id "string"]  \
                          ALIAS

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   MinIO 部署的 :ref:`alias <alias>`，以及要为其生成下载 URL 的对象完整路径。例如：

   .. code-block:: shell

      mc share download play/mybucket/object.txt

   你可以在同一个或不同的 MinIO 部署上指定多个对象。例如：

   .. code-block:: shell

      mc share download play/mybucket/object.txt play/mybucket/otherobject.txt

   如果指定的是存储桶路径或存储桶前缀路径，则**必须**同时指定
   :mc-cmd:`~mc share download --recursive` 参数。例如：

   .. code-block:: shell

      mc share download --recursive play/mybucket/

      mc share download --recursive play/mybucket/myprefix/

.. mc-cmd:: --expire, E
   :optional:

   为所有生成的 URL 设置过期时间限制。
   
   指定一个格式为 ``##h##m##s`` 的字符串。例如：``12h34m56s`` 表示 URL
   生成后 12 小时 34 分 56 秒过期。

   默认为 ``168h``，即 168 小时（7 天）。

.. mc-cmd:: --recursive, r
   :optional:
   
   递归地为 :mc-cmd:`mc share download ALIAS` 存储桶或存储桶前缀中的所有对象生成 URL。
      
   如果任一 ``ALIAS`` 指向存储桶路径或存储桶前缀路径，则此参数为必需。

.. mc-cmd:: --version-id, vid
   :optional:

   .. include:: /includes/facts-versioning.rst
      :start-after: start-version-id-desc
      :end-before: end-version-id-desc

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

生成用于下载对象的 URL
~~~~~~~~~~~~~~~~~~~~~~

.. tab-set::

   .. tab-item:: 获取特定对象

      使用 :mc:`mc share download` 为对象生成支持 ``GET`` 请求的 URL：

      .. code-block:: shell
         :class: copyable

         mc share download --expire DURATION ALIAS/PATH

      - 将 :mc-cmd:`ALIAS <mc share download ALIAS>` 替换为 MinIO 部署的
        :ref:`alias <alias>`。

      - 将 :mc-cmd:`PATH <mc share download ALIAS>` 替换为 MinIO 部署上对象的路径。

      - 将 :mc-cmd:`DURATION <mc share download --expire>` 替换为 URL 的过期时长。
        例如，要设置为 30 天过期，请指定 ``30d``。

   .. tab-item:: 获取存储桶中的对象

      使用 :mc:`mc share download` 并结合
      :mc-cmd:`~mc share download --recursive` 选项，可为存储桶中的每个对象生成一个 URL。
      每个 URL 都支持对其关联对象执行 ``GET`` 请求：

      .. code-block:: shell
         :class: copyable

         mc share download --recursive --expire DURATION ALIAS/PATH

      - 将 :mc-cmd:`ALIAS <mc share download ALIAS>` 替换为 MinIO 部署的
        :ref:`alias <alias>`。

      - 将 :mc-cmd:`PATH <mc share download ALIAS>` 替换为 MinIO 部署上的存储桶路径或存储桶前缀路径。

      - 将 :mc-cmd:`DURATION <mc share download --expire>` 替换为 URL 的过期时长。
        例如，要设置为 30 天过期，请指定 ``30d``。

行为
----

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
