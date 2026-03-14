====================
``mc admin kms key``
====================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc admin kms key

描述
----

.. start-mc-admin-kms-key-desc

:mc-cmd:`mc admin kms key` 命令通过 MinIO Key Encryption Service (KES)
执行加密密钥管理操作。

.. end-mc-admin-kms-key-desc

.. admonition:: 仅在 MinIO 部署上使用 ``mc admin``
   :class: note

   .. include:: /includes/facts-mc-admin.rst
      :start-after: start-minio-only
      :end-before: end-minio-only

.. TODO

   作为 KES 文档的一部分，后续需要回到本节继续完善。这里有大量内容
   只有在能够链接到 KES 概览与配置后才有意义。

语法
----

.. mc-cmd:: create
   :fullpath:


   在 Key Management System (KMS) 上创建一个新的主密钥。

   该命令语法如下：

   .. code-block:: shell
      :class: copyable

      mc admin kms key create TARGET [KEY_NAME]

   该命令接受以下参数：

   .. mc-cmd:: TARGET

      指定已配置 MinIO 部署的 :mc-cmd:`alias <mc alias>`。
      
      ``TARGET`` 部署**必须**包含已配置的
      MinIO Key Encryption Service (KES) 服务器。

   .. mc-cmd:: KEY_NAME

      指定新主密钥的名称。

.. mc-cmd:: status
   :fullpath:

   请求 Key Management System (KMS) 主密钥的信息。

   该命令语法如下：

   .. code-block:: shell
      :class: copyable

      mc admin kms key status TARGET [KEY_NAME]

   该命令接受以下参数：

   .. mc-cmd:: TARGET

      指定已配置 MinIO 部署的 :mc-cmd:`alias <mc alias>`。

      ``TARGET`` 部署**必须**包含已配置的 MinIO Key
      Encryption Service (KES) 服务器。

   .. mc-cmd:: KEY_NAME

      指定 KMS 上主密钥的名称。

      省略此参数可返回
      :mc-cmd:`~mc admin kms key status TARGET` 所指部署上的默认主密钥。

.. mc-cmd:: list
   :fullpath:

   列出 MinIO 实例的所有 Key Management System (KMS) 密钥。

   该命令语法如下：

   .. code-block:: shell
      :class: copyable

      mc admin kms key list TARGET

   该命令接受以下参数：

   .. mc-cmd:: TARGET

      指定已配置 MinIO 部署的 :mc-cmd:`alias <mc alias>`。

      ``TARGET`` 部署**必须**包含已配置的 MinIO Key Encryption Service (KES) 服务器。
