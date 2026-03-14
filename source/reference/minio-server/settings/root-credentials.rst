.. _minio-server-envvar-root:

====================
Root 访问设置
====================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

本页介绍用于控制 MinIO 进程 root（超级用户）访问权限的设置。
root 用户具有完整访问权限，可在 MinIO 部署上执行操作。

即使使用 :kes-docs:`MinIO Key Encryption Service <>` 或其他密钥管理工具，也必须配置 Root User 和 Root Password。

.. include:: /includes/common-mc-admin-config.rst
   :start-after: start-minio-settings-defined
   :end-before: end-minio-settings-defined

.. include:: /includes/common-mc-admin-config.rst
   :start-after: start-minio-settings-test-before-prod
   :end-before: end-minio-settings-test-before-prod

Root User
---------

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_ROOT_USER

         :ref:`root <minio-users-root>` 用户的访问密钥。

      .. warning::
   
         如果未设置 :envvar:`MINIO_ROOT_USER`，:mc:`minio` 默认使用 ``minioadmin``。
   
         在生产环境中**绝不要**使用默认凭证。
         MinIO 强烈建议在所有环境中为 :envvar:`MINIO_ROOT_USER` 指定唯一、足够长且随机的值。

   .. tab-item:: 配置项
      :sync: config

      此设置没有对应的配置变量。
      请改用环境变量。

Root Password
-------------

.. tab-set::

   .. tab-item:: 环境变量
      :selected:

      .. envvar:: MINIO_ROOT_PASSWORD

         :ref:`root <minio-users-root>` 用户的密钥。

      .. warning::

         如果未设置 :envvar:`MINIO_ROOT_PASSWORD`，:mc:`minio` 默认使用 ``minioadmin``。

         在生产环境中**绝不要**使用默认凭证。
         MinIO 强烈建议在所有环境中为 :envvar:`MINIO_ROOT_PASSWORD` 指定唯一、足够长且随机的值。

   .. tab-item:: 配置项

      此设置没有对应的配置变量。
      请改用环境变量。

.. _minio-disable-root-access:

Root 访问
-----------

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_API_ROOT_ACCESS

   .. tab-item:: 配置项
      :sync: config
  
      .. mc-conf:: api root-access
         :delimiter: " "

.. versionadded:: MinIO Server RELEASE.2023-05-04T21-44-30Z

设置为 ``on`` 可启用 :ref:`root <minio-users-root>` 用户账号，设置为 ``off`` 可禁用该账号。
禁用 root 服务账户时，也会禁用所有与 root 关联的服务账户，但用于站点复制的账号除外。
默认值为 ``on``。

.. important::

   如果通过此设置禁用 root API 访问，你**仍然必须**为内部使用设置 root user 和 root password。

在禁用 root 账号之前，请确保至少存在一个其他管理员用户，例如具备 :userpolicy:`consoleAdmin` 策略的用户。
如果没有其他管理员用户，禁用 root 账号会导致该部署的管理访问被锁定。

你可以使用此变量临时覆盖配置项，重新启用对部署的 root 访问。

如果发生意外锁定，要进行重置，请将 :envvar:`MINIO_API_ROOT_ACCESS` 设为 ``on`` 以覆盖此设置，并临时重新启用 root 账号。
随后你可以将此设置改为 ``on``，*或* 完成必要的用户/策略调整，以确保可通过其他非 root 账号进行正常管理访问。

唯一 Root 凭证
-----------------------

.. include:: /includes/common/common-deploy.rst
   :start-after: start-common-deploy-create-unique-root-credentials 
   :end-before: end-common-deploy-create-unique-root-credentials
