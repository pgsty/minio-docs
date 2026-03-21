.. _minio-server-envvar-mc:

=================
MinIO 客户端设置
=================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

本页面介绍 :ref:`MinIO Client <minio-client>` 的设置。

.. include:: /includes/common-mc-admin-config.rst
   :start-after: start-minio-settings-defined
   :end-before: end-minio-settings-defined

设置
----

主机凭证
~~~~~~~~

使用此设置可为 `mc` 命令添加一个临时别名。
例如，可用于脚本场景。

该临时别名使用 :aws-docs:`AWS s3v4 signature <AmazonS3/latest/API/sig-v4-authenticating-requests.html>`。

.. tab-set::

   .. tab-item:: 环境变量
      :selected:

      .. envvar:: MC_HOST_<ALIAS>

         将环境变量末尾的 ``<ALIAS>`` 替换为要设置主机的 ``alias``。

   .. tab-item:: 配置项

      .. include:: /includes/common-mc-admin-config.rst
         :start-after: start-minio-settings-no-config-option
         :end-before: end-minio-settings-no-config-option

      使用 :mc:`mc alias set` 配置 :ref:`alias <alias>`。

示例
++++

**静态凭证**

.. tab-set::

   .. tab-item:: 语法

      .. code-block:: shell
         :class: copyable

         export MC_HOST_<alias>=https://<Access Key>:<Secret Key>@<YOUR-S3-ENDPOINT>

   .. tab-item:: 示例

      .. code-block:: shell
         :class: copyable

         export MC_HOST_myalias=https://Q3AM3UQ867SPQQA43P2F:zuf+tfteSlswRu7BJ86wekitnifILbZam1KYY3TG@play.min.io

**Security Token Service (STS) 凭证**

.. tab-set::

   .. tab-item:: 语法

      .. code-block:: shell
         :class: copyable

         export MC_HOST_<alias>=https://<Access Key>:<Secret Key>:<Session Token>@<YOUR-S3-ENDPOINT>

   .. tab-item:: 示例

      .. code-block:: shell
         :class: copyable

         export MC_HOST_myalias=https://Q3AM3UQ867SPQQA43P2F:zuf+tfteSlswRu7BJ86wekitnifILbZam1KYY3TG:eyJhbGciOiJIUzUxMiIsInR5cCI6IkpXVCJ9.eyJhY2Nlc3NLZXkiOiJOVUlCT1JaWVRWMkhHMkJNUlNYUiIsImF1ZCI6IlBvRWdYUDZ1Vk80NUlzRU5SbmdEWGo1QXU1WWEiLCJhenAiOiJQb0VnWFA2dVZPNDVJc0VOUm5nRFhqNUF1NVlhIiwiZXhwIjoxNTM0ODk2NjI5LCJpYXQiOjE1MzQ4OTMwMjksImlzcyI6Imh0dHBzOi8vbG9jYWxob3N0Ojk0NDMvb2F1dGgyL3Rva2VuIiwianRpIjoiNjY2OTZjZTctN2U1Ny00ZjU5LWI0MWQtM2E1YTMzZGZiNjA4In0.eJONnVaSVHypiXKEARSMnSKgr-2mlC2Sr4fEGJitLcJF_at3LeNdTHv0_oHsv6ZZA3zueVGgFlVXMlREgr9LXA@play.min.io

STS 服务
~~~~~~~~

.. versionadded:: mc RELEASE.2023-11-06T04-19-23Z

使用此设置可添加一个用于 `mc` 命令的 STS endpoint。

.. versionchanged:: mc RELEASE.2023-12-02T02-03-28Z

支持按 alias 添加多个环境变量。

.. tab-set::

   .. tab-item:: 环境变量
      :selected:

      .. envvar:: MC_STS_ENDPOINT_<alias>

      .. code-block:: shell

         export MC_STS_ENDPOINT_myalias=https://sts.minio-operator.svc.cluster.local:4223/sts/ns-1

   .. tab-item:: 配置项

      .. include:: /includes/common-mc-admin-config.rst
         :start-after: start-minio-settings-no-config-option
         :end-before: end-minio-settings-no-config-option

Web Token Identity
~~~~~~~~~~~~~~~~~~

.. versionadded:: mc RELEASE.2023-11-06T04-19-23Z

使用此设置可添加一个用于 `mc` 命令的 Web Token Identity。

.. versionchanged:: mc RELEASE.2023-12-02T02-03-28Z

支持按 alias 添加多个环境变量。

.. tab-set::

   .. tab-item:: 环境变量
      :selected:

      .. envvar:: MC_WEB_IDENTITY_TOKEN_<alias>

      .. code-block:: shell

         export MC_WEB_IDENTITY_TOKEN_FILE_myalias=/var/run/secrets/kubernetes.io/serviceaccount/token

   .. tab-item:: 配置项

      .. include:: /includes/common-mc-admin-config.rst
         :start-after: start-minio-settings-no-config-option
         :end-before: end-minio-settings-no-config-option

配置目录
~~~~~~~~

指定 MinIO Client 应使用的配置目录路径。

.. tab-set::

   .. tab-item:: 环境变量
      :selected:

      .. envvar:: MC_CONFIG_DIR

   .. tab-item:: 配置项

      .. include:: /includes/common-mc-admin-config.rst
         :start-after: start-minio-settings-no-config-option
         :end-before: end-minio-settings-no-config-option

进度条
~~~~~~

禁用 MinIO Client 进度条。

.. tab-set::

   .. tab-item:: 环境变量
      :selected:

      .. envvar:: MC_QUIET

   .. tab-item:: 配置项

      .. include:: /includes/common-mc-admin-config.rst
         :start-after: start-minio-settings-no-config-option
         :end-before: end-minio-settings-no-config-option

Pager
~~~~~

.. versionadded:: mc RELEASE.2024-04-29T09-56-05Z

在 CLI 中禁用 MinIO Client 的分页器功能。
使用该设置后，输出将改为直接打印到原始 ``STDOUT``。

.. tab-set::

   .. tab-item:: 环境变量
      :selected:

      .. envvar:: MC_DISABLE_PAGER

   .. tab-item:: 配置项

      .. include:: /includes/common-mc-admin-config.rst
         :start-after: start-minio-settings-no-config-option
         :end-before: end-minio-settings-no-config-option

颜色主题
~~~~~~~~

禁用 MinIO Client 输出使用的颜色主题。

.. tab-set::

   .. tab-item:: 环境变量
      :selected:

      .. envvar:: MC_NO_COLOR

   .. tab-item:: 配置项

      .. include:: /includes/common-mc-admin-config.rst
         :start-after: start-minio-settings-no-config-option
         :end-before: end-minio-settings-no-config-option


JSON
~~~~

启用将输出格式化为 JSON lines。

.. tab-set::

   .. tab-item:: 环境变量
      :selected:

      .. envvar:: MC_JSON

   .. tab-item:: 配置项

      .. include:: /includes/common-mc-admin-config.rst
         :start-after: start-minio-settings-no-config-option
         :end-before: end-minio-settings-no-config-option

调试
~~~~

启用调试输出。

.. tab-set::

   .. tab-item:: 环境变量
      :selected:

      .. envvar:: MC_DEBUG

   .. tab-item:: 配置项

      .. include:: /includes/common-mc-admin-config.rst
         :start-after: start-minio-settings-no-config-option
         :end-before: end-minio-settings-no-config-option

禁用 SSL
~~~~~~~~

禁用 SSL 证书校验。

.. tab-set::

   .. tab-item:: 环境变量
      :selected:

      .. envvar:: MC_INSECURE

   .. tab-item:: 配置项

      .. include:: /includes/common-mc-admin-config.rst
         :start-after: start-minio-settings-no-config-option
         :end-before: end-minio-settings-no-config-option

限制下载带宽
~~~~~~~~~~~~

限制 MinIO Client 在某些命令中使用的下载带宽。

.. tab-set::

   .. tab-item:: 环境变量
      :selected:

      .. envvar:: MC_LIMIT_DOWNLOAD

   .. tab-item:: 配置项

      .. include:: /includes/common-mc-admin-config.rst
         :start-after: start-minio-settings-no-config-option
         :end-before: end-minio-settings-no-config-option

若未指定，MinIO Client 使用全部可用带宽。

将客户端侧下载速率限制为不超过指定值（KiB/s、MiB/s 或 GiB/s）。该设置仅影响运行 MinIO Client 的本地设备发起的下载。支持的单位包括：

- B 表示 bytes
- K 表示 kilobytes
- M 表示 megabytes
- G 表示 gigabytes
- Ki 表示 kibibytes
- Mi 表示 mibibytes
- Gi 表示 gibibytes

例如，要将下载速率限制为不超过 1 GiB/s，可在 Linux 系统上使用以下命令：

.. code-block:: shell
   :class: copyable

   export MC_LIMIT_DOWNLOAD=1G

在非 Linux 系统上，请参考你的操作系统文档使用等效命令。

限制上传带宽
~~~~~~~~~~~~

限制 MinIO Client 在某些命令中使用的上传带宽。

.. tab-set::

   .. tab-item:: 环境变量
      :selected:

      .. envvar:: MC_LIMIT_UPLOAD

   .. tab-item:: 配置项

      .. include:: /includes/common-mc-admin-config.rst
         :start-after: start-minio-settings-no-config-option
         :end-before: end-minio-settings-no-config-option

若未指定，MinIO Client 使用全部可用带宽。

将客户端侧上传速率限制为不超过指定值（KiB/s、MiB/s 或 GiB/s）。该设置仅影响运行 MinIO Client 的本地设备发起的上传。支持的单位包括：

- B 表示 bytes
- K 表示 kilobytes
- M 表示 megabytes
- G 表示 gigabytes
- Ki 表示 kibibytes
- Mi 表示 mibibytes
- Gi 表示 gibibytes

例如，要将上传速率限制为不超过 1 GiB/s，可在 Linux 系统上使用以下命令：

.. code-block:: shell
   :class: copyable

   export MC_LIMIT_UPLOAD=1G

在非 Linux 系统上，请参考你的操作系统文档使用等效命令。

SSE-KMS 加密
~~~~~~~~~~~~

使用服务端管理密钥通过 :ref:`SSE-KMS <minio-sse-data-encryption>` 对选项进行加密和解密。

.. tab-set::

   .. tab-item:: 环境变量
      :selected:

      .. envvar:: MC_ENC_KMS

         使用 :envvar:`MC_ENC_KMS` 环境变量指定密钥。

   .. tab-item:: 配置项

      .. include:: /includes/common-mc-admin-config.rst
         :start-after: start-minio-settings-no-config-option
         :end-before: end-minio-settings-no-config-option

SSE-S3 加密
~~~~~~~~~~~

使用服务端管理密钥通过 :ref:`SSE-KMS <minio-sse-data-encryption>` 对选项进行加密和解密。

.. tab-set::

   .. tab-item:: 环境变量
      :selected:

      .. envvar:: MC_ENC_S3

         指定执行 SSE-S3 加密时使用的密钥。
         指定值必须与 :envvar:`MINIO_KMS_KES_KEY_NAME` 中设置的加密密钥匹配。

   .. tab-item:: 配置项

      .. include:: /includes/common-mc-admin-config.rst
         :start-after: start-minio-settings-no-config-option
         :end-before: end-minio-settings-no-config-option
