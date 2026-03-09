.. _minio-mc-alias-set:
.. _minio-mc-alias:
.. _alias:

================
``mc alias set``
================

.. default-domain:: minio

.. contents:: Table of Contents
   :local:
   :depth: 2

.. mc:: mc alias set

.. |command| replace:: :mc:`mc alias set`

语法
----

.. start-mc-alias-set-desc

:mc:`mc alias set` 命令用于在本地 :program:`mc` 配置中添加或更新别名。

.. end-mc-alias-set-desc

.. tab-set::

   .. tab-item:: 示例

      以下命令为运行在 URL ``https://myminio.example.net`` 的 MinIO
      部署 ``myminio`` 添加一个 :ref:`alias <alias>`。:program:`mc` 使用指定的用户名和密码对该 MinIO 部署进行身份验证：

      .. code-block:: shell
         :class: copyable
         
         mc alias set myminio https://myminio.example.net minioadminuser minioadminpassword

      如果 ``myminio`` 别名已存在，该命令会使用新的 URL、access key 和
      secret key 覆盖该别名。

   .. tab-item:: 语法

      :mc:`mc alias set` 命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] alias set \ 
                          [--api "string"]                           \
                          [--path "string"]                          \
                          ALIAS                                      \ 
                          URL                                        \
                          ACCESSKEY                                  \
                          SECRETKEY

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~

.. mc-cmd:: ALIAS

   *必填* 与 S3 兼容服务关联的名称。
   别名区分大小写，且必须满足以下要求：

   - 只能包含 `ASCII <https://en.wikipedia.org/wiki/ASCII>`__ 小写字母（``a-z``）、大写字母（``A-Z``）、数字（``[0-9]``）、连字符（``-``）或下划线（``_``）。
   - 长度为 2 个或更多字符。
   - 首字符必须是字母。

   .. versionchanged:: RELEASE.2024-01-11T05-49-32Z

      别名也可以是单个字母（``a-z`` 或 ``A-Z``）。

   部分有效别名示例如下：

   - ``myminio``
   - ``Test-1``
   - ``A``
   - ``a``

.. mc-cmd:: URL

   *必填* S3 兼容服务端点的 URL。例如：

   ``https://minio.example.net``

.. mc-cmd:: ACCESSKEY
   
   *必填*

   用于对 S3 服务进行身份验证的 access key。

.. mc-cmd:: SECRETKEY

   *必填*

   用于对 S3 服务进行身份验证的 secret key。

.. mc-cmd:: --api
   
   
   *可选*

   指定连接到 S3 兼容服务时使用的签名计算方法。支持以下值：

   - ``S3v4``（默认）
   - ``S3v2``

   .. note::

      AWS 将 AWS Signature V2 视为
      `deprecated <https://aws.amazon.com/blogs/aws/amazon-s3-update-sigv2-deprecation-period-extended-modified/>`__。
      :mc:`mc alias set` 保留该选项，仅用于仍依赖 Signature V2 的 S3 存储桶或服务。
      
      除非 S3 兼容服务明确要求，否则请使用 ``S3v4``。
      MinIO server 不依赖也不要求 ``S3v2``，且并非所有 API 操作都可在 ``S3v2`` 上使用。

.. mc-cmd:: --path
   

   *可选*

   指定服务端使用的存储桶路径查找设置。支持以下值：

   - ``"auto"``（默认）
   - ``"on"``
   - ``"off"``

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

为 MinIO 部署添加或更新别名
~~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc:`mc alias set` 添加一个供 :program:`mc` 使用的 S3 兼容服务：

.. tab-set::

   .. tab-item:: 示例

      以下命令创建一个新的别名 ``myminio``，指向位于
      ``https://minio.example.net`` 的 MinIO 部署。该别名使用
      ``miniouser`` 和 ``miniopassword`` 凭据对该部署执行操作。

      .. code-block:: shell
         :class: copyable

         mc alias set myminio https://minio.example.net miniouser miniopassword

      如果 ``myminio`` 别名已存在，
      :mc:`mc alias set` 命令会使用指定参数覆盖该别名。

   .. tab-item:: 语法

      .. code-block:: shell
         :class: copyable

         mc alias set ALIAS HOSTNAME ACCESSKEY SECRETKEY

      - 将 ``ALIAS`` 替换为与 MinIO 服务关联的名称。

      - 将 ``HOSTNAME`` 替换为 MinIO 部署中任意节点的 URL。你也可以指定
        用于管理 MinIO 部署连接的负载均衡器或反向代理 URL。

      - 将 ``ACCESSKEY`` 和 ``SECRETKEY`` 替换为 MinIO 部署中某个用户的凭据。

行为
----

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility

所需凭据与访问控制
~~~~~~~~~~~~~~~~~~

:mc:`mc alias set` 要求为 S3 兼容主机指定 access key 及对应的
secret key。:program:`mc` 的功能受指定凭据关联策略的限制。例如，如果
指定凭据对某个存储桶没有读写权限，:program:`mc` 就无法对该存储桶执行读写操作。

有关 MinIO 访问控制的更多信息，请参阅
:ref:`minio-access-management`。

有关 S3 访问控制的更完整文档，请参阅
:s3-docs:`Amazon S3 Security <security.html>`.

对于其他所有 S3 兼容服务，请参考对应服务的文档。

证书
~~~~

MinIO Client 会获取对端证书、计算公钥指纹，并询问用户是否接受该部署的证书。

如果被信任，MinIO Client 会自动将证书颁发机构添加到：

-  Linux 和其他类 Unix 系统上的 ``~/.mc/certs/CAs/``。
-  Windows 系统上的 ``C:\Users\[username]\mc\certs\CAs\``。
