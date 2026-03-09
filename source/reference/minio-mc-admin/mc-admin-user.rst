=================
``mc admin user``
=================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc admin user

说明
----

.. start-mc-admin-user-desc

:mc:`mc admin user` 命令及其子命令用于管理 :ref:`MinIO 用户 <minio-internal-idp>`。

.. end-mc-admin-user-desc

客户端 *必须* 使用与该部署中某个用户关联的 access key 和 secret key 对 MinIO 部署进行认证。
MinIO 用户是 MinIO 身份与访问管理中的关键组成部分。

要管理使用第三方 IDP 进行认证的用户，请使用对应提供方的命令：

- 对于 AD/LDAP，请使用 :mc:`mc idp ldap`
- 对于兼容 OpenID Connect (OIDC) 的提供方，请使用 :mc:`mc idp openid`

.. admonition:: 仅在 MinIO 部署上使用 ``mc idp`` 命令
   :class: note

   :mc:`mc idp ldap` 和 :mc:`mc idp openid` 及其子命令仅支持对 MinIO 部署使用。


子命令
------

:mc:`mc admin user` 包含以下子命令：

.. list-table::
   :header-rows: 1
   :widths: 30 70
   :width: 100%

   * - 子命令
     - 说明

   * - :mc:`~mc admin user add`
     - .. include:: /reference/minio-mc-admin/mc-admin-user-add.rst
          :start-after: start-mc-admin-user-add-desc
          :end-before: end-mc-admin-user-add-desc

   * - :mc:`~mc admin user disable`
     - .. include:: /reference/minio-mc-admin/mc-admin-user-disable.rst
          :start-after: start-mc-admin-user-disable-desc
          :end-before: end-mc-admin-user-disable-desc

   * - :mc:`~mc admin user enable`
     - .. include:: /reference/minio-mc-admin/mc-admin-user-enable.rst
          :start-after: start-mc-admin-user-enable-desc
          :end-before: end-mc-admin-user-enable-desc

   * - :mc:`~mc admin user info`
     - .. include:: /reference/minio-mc-admin/mc-admin-user-info.rst
          :start-after: start-mc-admin-user-info-desc
          :end-before: end-mc-admin-user-info-desc

   * - :mc:`~mc admin user ls`
     - .. include:: /reference/minio-mc-admin/mc-admin-user-list.rst
          :start-after: start-mc-admin-user-list-desc
          :end-before: end-mc-admin-user-list-desc

   * - :mc:`~mc admin user rm`
     - .. include:: /reference/minio-mc-admin/mc-admin-user-remove.rst
          :start-after: start-mc-admin-user-remove-desc
          :end-before: end-mc-admin-user-remove-desc

   * - :mc-cmd:`sts info <mc admin user sts info>`
     - .. include:: /reference/minio-mc-admin/mc-admin-user-sts-info.rst
          :start-after: start-mc-admin-sts-info-desc
          :end-before: end-mc-admin-sts-info-desc

   * - :mc:`~mc admin user svcacct`
     - .. include:: /reference/minio-mc-admin/mc-admin-user-svcacct.rst
          :start-after: start-mc-admin-user-svcacct-desc
          :end-before: end-mc-admin-user-svcacct-desc

.. toctree::
   :titlesonly:
   :hidden:
   
   /reference/minio-mc-admin/mc-admin-user-add
   /reference/minio-mc-admin/mc-admin-user-disable
   /reference/minio-mc-admin/mc-admin-user-enable
   /reference/minio-mc-admin/mc-admin-user-info
   /reference/minio-mc-admin/mc-admin-user-list
   /reference/minio-mc-admin/mc-admin-user-remove
   /reference/minio-mc-admin/mc-admin-user-sts-info
   /reference/minio-mc-admin/mc-admin-user-svcacct
