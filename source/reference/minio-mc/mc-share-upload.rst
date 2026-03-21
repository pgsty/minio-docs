===================
``mc share upload``
===================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc share upload

语法
-----------

.. start-mc-share-upload-desc

:mc:`mc share upload` 命令会生成一个临时的预签名 URL，并集成用于将对象上传到 MinIO 存储桶的访问凭证。该临时 URL 会在可配置的时间限制后过期。

.. end-mc-share-upload-desc

应用程序可以通过该 URL 执行 ``PUT`` 以上传对象。

有关可共享对象 URL 的更多信息，请参阅 Amazon S3 文档中的 :aws-docs:`Pre-Signed URLs
<AmazonS3/latest/dev/ShareObjectPreSignedURL.html>`。

.. tab-set::

   .. tab-item:: 示例

      以下命令会为 ``myminio`` MinIO 部署上的 ``mydata`` 存储桶生成一个新的预签名上传 URL：

      .. code-block:: shell
         :class: copyable

         mc share upload --recursive myminio/mydata

   .. tab-item:: 语法

      该命令的语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] share upload               \
                          [--content-type "string"]  \
                          [--expire "string"]        \
                          [--recursive]              \
                          ALIAS

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~~~~~~~

.. mc-cmd:: ALIAS

   *Required* MinIO 部署的 :ref:`alias <alias>` 以及要为其生成上传 URL 的对象完整路径。例如：

   .. code-block:: shell

      mc share upload play/mybucket/object.txt

   你可以在同一个或不同的 MinIO 部署上指定多个对象。例如：

   .. code-block:: shell

      mc share upload play/mybucket/object.txt play/mybucket/otherobject.txt

   如果指定的是存储桶路径或存储桶前缀路径，则你**必须**同时指定 :mc-cmd:`~mc share upload --recursive` 参数。例如：

   .. code-block:: shell

      mc share upload --recursive play/mybucket/

      mc share upload --recursive play/mybucket/myprefix/

.. mc-cmd:: --content-type, T

   *Optional* 将上传限制为仅接受带有特定 `Content-Type <https://www.w3.org/Protocols/rfc1341/4_Content-Type.html>`__ 头的请求。

   指定一个字符串作为可接受的 ``Content-Type`` 值。
   例如，``video/mp4``。

   配置后，使用该生成 URL 的客户端必须包含指定类型的 ``Content-Type`` 头。
   MinIO 会拒绝未携带正确 ``Content-Type`` 头的请求。

   Content types 也称为 `media types <https://www.iana.org/assignments/media-types/media-types.xhtml>`__。

.. mc-cmd:: --expire, E


   *Optional* 为所有生成的 URL 设置过期时限。

   指定格式为 ``##h##m##s`` 的字符串。例如：
   ``12h34m56s`` 表示在 URL 生成后 12 小时 34 分 56 秒过期。

   默认值为 ``168h``，即 168 小时（7 天）。

.. mc-cmd:: --recursive, r


   *Optional* 修改 CURL URL 以支持将对象上传到存储桶或存储桶前缀。
   如果任意 ``ALIAS`` 指定的是存储桶路径或存储桶前缀路径，则该选项为必需。修改后的 CURL 输出如下所示：

   .. code-block:: shell

      curl ... -F key=<NAME> -F file=@<FILE>

   将 ``<FILE>`` 替换为要上传的文件路径。

   将 ``<NAME>`` 替换为上传后的对象名称。
   该名称可以包含 :term:`prefixes <prefix>`。

全局参数
~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
--------

生成用于上传对象的 URL
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tab-set::

   .. tab-item:: 上传单个对象

      使用 :mc:`mc share upload` 生成一个支持 ``POST`` 请求的 URL，
      用于将文件上传到 MinIO 部署上的特定对象位置：

      .. code-block:: shell
         :class: copyable

         mc share upload --expire DURATION ALIAS/PATH

      - 将 :mc-cmd:`ALIAS <mc share upload ALIAS>` 替换为 MinIO 部署的
        :ref:`alias <alias>`。

      - 将 :mc-cmd:`PATH <mc share upload ALIAS>` 替换为 MinIO 部署中对象的路径。

      - 将 :mc-cmd:`DURATION <mc share upload --expire>` 替换为 URL 过期前的时长。
        例如，要设置 30 天过期时间，指定 ``30d``。

   .. tab-item:: 上传多个对象

      使用 :mc:`mc share upload` 并结合
      :mc-cmd:`~mc share upload --recursive` 与
      :mc-cmd:`~mc share upload --expire` 选项，生成一个临时 URL，
      该 URL 支持 ``POST`` 请求，用于将文件上传到 MinIO 部署上的存储桶：

      .. code-block:: shell
         :class: copyable

         mc share upload --recursive --expire DURATION ALIAS/PATH

      - 将 :mc-cmd:`ALIAS <mc share upload ALIAS>` 替换为 MinIO 部署的
        :ref:`alias <alias>`。

      - 将 :mc-cmd:`PATH <mc share upload ALIAS>` 替换为 MinIO 部署中的存储桶或存储桶前缀路径。

      - 将 :mc-cmd:`DURATION <mc share upload --expire>` 替换为 URL 过期前的时长。
        例如，要设置 30 天过期时间，指定 ``30d``。

      该命令会返回一个用于向指定存储桶前缀上传对象的 CURL 命令。

      - 将返回的 CURL 命令中的 ``<FILE>`` 字符串替换为要上传的文件路径。

      - 将返回的 CURL 命令中的 ``<NAME>`` 字符串替换为存储桶中的对象名称。
        该名称可以包含 :term:`prefixes <prefix>`。

      你可以使用 shell 脚本循环将文件系统目录中的内容递归上传到兼容 S3 的服务：

      .. code-block:: shell

         #!/bin/sh

         for file in ~/Documents/photos/
         do
            curl https://play.min.io/mybucket/ \
            -F policy=AAAAA -F x-amz-algorithm=AWS4-HMAC-SHA256 \
            -F x-amz-credential=AAAA/us-east-1/s3/aws4_request \
            -F x-amz-date=20200812T202556Z \
            -F x-amz-signature=AAAA \
            -F bucket=mybucket -F key=photos/${file} -F file=@${file}

         done

      此示例会将 ``~/Documents/photos/`` 目录中的每个文件上传到 ``mybucket`` 存储桶下的 ``photos`` 前缀。
      对于如何遍历目录中的文件，请遵循你所选脚本语言文档中的最佳实践。

行为
--------

S3 兼容性
~~~~~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
