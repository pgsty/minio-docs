.. _minio-mc-admin-accesskey:

======================
``mc admin accesskey``
======================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc admin accesskey

.. versionadded:: MinIO客户端版本RELEASE.2024-10-08T09-37-26Z

这些命令用于替代 :mc:`mc admin user svcacct` 命令及其子命令中的 MinIO IDP 功能。

描述
----

.. start-mc-admin-accesskey-desc

:mc:`mc admin accesskey` 命令及其子命令用于为 MinIO 部署中内部管理的用户创建和管理 :ref:`Access Keys <minio-idp-service-account>`。

.. end-mc-admin-accesskey-desc

每个访问密钥都关联到一个 :ref:`用户身份 <minio-authentication-and-identity-management>`，并继承其父用户直接附加的 :ref:`策略 <minio-policy>` *或* 父用户所属组的策略。
每个访问密钥还支持可选的内联策略，可进一步将访问限制为父用户可用操作和资源的一个子集。

:mc:`mc admin user svcacct` 仅支持为 :ref:`MinIO-managed <minio-users>` 账户创建访问密钥。

要为 :ref:`Active Directory/LDAP-managed <minio-external-identity-management-ad-ldap>` 账户创建访问密钥，请使用 :mc:`mc idp ldap accesskey` 及其子命令。
要管理 :ref:`OpenID Connect-managed users <minio-external-identity-management-openid>` 的访问密钥，请登录 :ref:`MinIO Console <minio-console>` 并通过 UI 生成访问密钥。

:mc:`mc admin accesskey` 命令包含以下子命令：

.. list-table::
   :header-rows: 1
   :widths: 40 60

   * - 子命令
     - 描述

   * - :mc:`~mc admin accesskey create`
     - .. include:: /reference/minio-mc-admin/mc-admin-accesskey-create.rst
          :start-after: start-mc-admin-accesskey-create-desc
          :end-before: end-mc-admin-accesskey-create-desc

   * - :mc:`~mc admin accesskey disable`
     - .. include:: /reference/minio-mc-admin/mc-admin-accesskey-disable.rst
          :start-after: start-mc-admin-accesskey-disable-desc
          :end-before: end-mc-admin-accesskey-disable-desc

   * - :mc:`~mc admin accesskey edit`
     - .. include:: /reference/minio-mc-admin/mc-admin-accesskey-edit.rst
          :start-after: start-mc-admin-accesskey-edit-desc
          :end-before: end-mc-admin-accesskey-edit-desc

   * - :mc:`~mc admin accesskey enable`
     - .. include:: /reference/minio-mc-admin/mc-admin-accesskey-enable.rst
          :start-after: start-mc-admin-accesskey-enable-desc
          :end-before: end-mc-admin-accesskey-enable-desc

   * - :mc:`~mc admin accesskey info`
     - .. include:: /reference/minio-mc-admin/mc-admin-accesskey-info.rst
          :start-after: start-mc-admin-accesskey-info-desc
          :end-before: end-mc-admin-accesskey-info-desc

   * - :mc:`~mc admin accesskey ls`
     - .. include:: /reference/minio-mc-admin/mc-admin-accesskey-list.rst
          :start-after: start-mc-admin-accesskey-list-desc
          :end-before: end-mc-admin-accesskey-list-desc

   * - :mc:`~mc admin accesskey rm`
     - .. include:: /reference/minio-mc-admin/mc-admin-accesskey-remove.rst
          :start-after: start-mc-admin-accesskey-remove-desc
          :end-before: end-mc-admin-accesskey-remove-desc


.. toctree::
   :titlesonly:
   :hidden:

   /reference/minio-mc-admin/mc-admin-accesskey-create
   /reference/minio-mc-admin/mc-admin-accesskey-disable
   /reference/minio-mc-admin/mc-admin-accesskey-edit
   /reference/minio-mc-admin/mc-admin-accesskey-enable
   /reference/minio-mc-admin/mc-admin-accesskey-info
   /reference/minio-mc-admin/mc-admin-accesskey-list
   /reference/minio-mc-admin/mc-admin-accesskey-remove
