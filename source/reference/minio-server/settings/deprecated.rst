.. _minio-server-envvar-deprecated:

===================
已弃用设置
===================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

本页介绍用于控制 MinIO 进程核心行为的已弃用设置。

本页中的设置可能随时被移除。
用户应尽早迁移到推荐的替代项。

.. include:: /includes/common-mc-admin-config.rst
   :start-after: start-minio-settings-defined
   :end-before: end-minio-settings-defined

.. include:: /includes/common-mc-admin-config.rst
   :start-after: start-minio-settings-test-before-prod
   :end-before: end-minio-settings-test-before-prod

环境变量
---------------------

以下*环境变量*已弃用。
此处仅为历史参考而列出。

.. envvar:: MINIO_SECRET_KEY

   .. deprecated:: RELEASE.2021-04-22T15-44-28Z

   :ref:`root <minio-users-root>` 用户的 secret key。

   该环境变量已*弃用*，请改用 :envvar:`MINIO_ROOT_PASSWORD` 环境变量。

   .. warning::

      如果未设置 :envvar:`MINIO_SECRET_KEY`，:mc:`minio` 默认使用 ``minioadmin``。

      在生产环境中**绝不要**使用默认凭证。
      MinIO 强烈建议在所有环境中为 :envvar:`MINIO_ACCESS_KEY` 指定唯一、足够长且随机的值。

.. envvar:: MINIO_ACCESS_KEY

   .. deprecated:: RELEASE.2021-04-22T15-44-28Z

   :ref:`root <minio-users-root>` 用户的 access key。

         该环境变量已*弃用*，请改用 :envvar:`MINIO_ROOT_USER` 环境变量。

   .. warning::

      如果未设置 :envvar:`MINIO_ACCESS_KEY`，:mc:`minio` 默认使用 ``minioadmin``。

      在生产环境中**绝不要**使用默认凭证。
      MinIO 强烈建议在所有环境中为 :envvar:`MINIO_ACCESS_KEY` 指定唯一、足够长且随机的值。

.. envvar:: MINIO_ACCESS_KEY_OLD

   .. deprecated:: RELEASE.2021-04-22T15-44-28Z

   要轮换 root 凭证，请修改 :envvar:`MINIO_ROOT_USER` 和 :envvar:`MINIO_ROOT_PASSWORD` 环境变量。

.. envvar:: MINIO_OPERATOR_DEPLOYMENT_NAME

   .. deprecated:: Operator 6.0.4

   指定为 Operator 创建并使用的命名空间。

   未指定时，默认值为 ``minio-operator``。

.. envvar:: MINIO_SECRET_KEY_OLD

   .. deprecated:: RELEASE.2021-04-22T15-44-28Z

   要轮换 root 凭证，请修改 :envvar:`MINIO_ROOT_USER` 和 :envvar:`MINIO_ROOT_PASSWORD` 环境变量。

.. envvar:: MINIO_SERVER_URL

   .. deprecated:: RELEASE.2024-05-10T01-41-38Z

   MinIO Console 用于连接 MinIO Server 的 `fully qualified domain name <https://en.wikipedia.org/wiki/Fully_qualified_domain_name>`__ (FQDN)。

   为确保 Console 正常工作，MinIO server URL *必须*是主机的 FQDN，且可解析并可达。

   如果指定值无法解析到 MinIO server，通过 MinIO Console 登录将失败，并在等待一段时间后返回网络错误。
   较旧版本的 Console 可能会返回通用的“Invalid Login”错误。
   可通过取消设置该值*或*解决 FQDN 解析问题来恢复 Console 登录。
   在以下情况下可能需要该设置：

   - MinIO Server 使用的 TLS 证书在 Subject Alternative Name (SAN) 中未包含主机本地 IP。

   或

   - 由于反向代理或类似配置，Console 必须使用特定主机名来连接或引用 MinIO Server。
