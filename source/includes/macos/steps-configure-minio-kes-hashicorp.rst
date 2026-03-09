部署启用服务器端加密的 MinIO 和 KES
---------------------------------

开始以下步骤前，请先创建这些目录：

.. code-block:: shell
   :class: copyable
   :substitutions:

   mkdir -p |kescertpath|
   mkdir -p |kesconfigpath|
   mkdir -p |miniodatapath|

前提条件
~~~~~~~~

根据你选择的 :kes-docs:`受支持 KMS 目标 <#supported-kms-targets>` 配置，
你可能需要将 ``kes-server.cert`` 作为受信任的 Certificate Authority (CA) 传递。
关于如何信任第三方 CA，请参阅客户端文档。

1) 创建 MinIO 配置
~~~~~~~~~~~~~~~~~~

创建 MinIO 环境文件

使用你偏好的文本编辑器创建环境文件。
以下示例使用 ``nano``：

.. code-block:: shell
   :substitutions:

   nano |minioconfigpath|/minio

.. include:: /includes/common/common-minio-kes.rst
   :start-after: start-kes-configuration-minio-desc
   :end-before: end-kes-configuration-minio-desc

3) 启动 MinIO Server
~~~~~~~~~~~~~~~~~~~~

.. note::

   你必须先启动 KES，再启动 MinIO。
   MinIO 部署在启动过程中需要访问 KES。

.. include:: /includes/common/common-minio-kes.rst
   :start-after: start-kes-minio-start-server-desc
   :end-before: end-kes-minio-start-server-desc

前台进程依赖其运行所在的 shell 或终端。
退出或终止该 shell/终端实例，也会一并结束所附着的进程。
关于如何将进程放到后台运行，请遵循你的操作系统最佳实践。

4) 生成新的加密密钥
~~~~~~~~~~~~~~~~~~

.. include:: /includes/common/common-minio-kes.rst
   :start-after: start-kes-generate-key-desc
   :end-before: end-kes-generate-key-desc

5) 为存储桶启用 SSE-KMS
~~~~~~~~~~~~~~~~~~~~~~

.. include:: /includes/common/common-minio-kes.rst
   :start-after: start-kes-enable-sse-kms-desc
   :end-before: end-kes-enable-sse-kms-desc
