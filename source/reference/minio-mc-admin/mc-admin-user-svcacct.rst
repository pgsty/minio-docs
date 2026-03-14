.. _minio-mc-admin-user-svcacct:

=========================
``mc admin user svcacct``
=========================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc admin user svcacct

.. important::

   这些命令已被替代，并将在未来的 MinIO 客户端版本中弃用。

   自 MinIO客户端版本RELEASE.2024-10-08T09-37-26Z 起，与内置 MinIO IDP 用户及其访问密钥或 STS 令牌相关的功能，请使用 :mc:`mc admin accesskey` 命令及其子命令。

   对于 AD/LDAP 用户的访问密钥，请使用 :mc:`mc idp ldap accesskey` 命令及其子命令。

描述
----

.. start-mc-admin-user-svcacct-desc

:mc:`mc admin user svcacct` 命令及其子命令用于在 MinIO 部署上创建和管理 :ref:`访问密钥 <minio-idp-service-account>`。

自 MinIO客户端版本RELEASE.2024-10-08T09-37-26Z 起，这些命令已由 :mc:`mc admin accesskey` 和 :mc:`mc idp ldap accesskey` 取代。
该命令及其子命令将在未来的 MinIO 客户端版本中弃用。

.. end-mc-admin-user-svcacct-desc

每个访问密钥都关联到一个 :ref:`用户身份 <minio-authentication-and-identity-management>`，并继承其父用户所附加的 :ref:`策略 <minio-policy>` *或* 父用户所属组的策略。
每个访问密钥还支持可选的内联策略，用于进一步将访问限制在父用户可用操作和资源的子集范围内。

:mc:`mc admin user svcacct` 仅支持为 :ref:`MinIO 托管 <minio-users>` 和 :ref:`Active Directory/LDAP 托管 <minio-external-identity-management-ad-ldap>` 账户创建访问密钥。 

如需为 :ref:`OpenID Connect 托管用户 <minio-external-identity-management-openid>` 创建访问密钥，请登录 :ref:`MinIO Console <minio-console>` 并通过 UI 生成访问密钥。

:mc:`mc admin user svcacct` 命令包含以下子命令：

.. list-table::
   :header-rows: 1
   :widths: 40 60

   * - 子命令
     - 描述

   * - :mc:`~mc admin user svcacct add`
     - .. include:: /reference/minio-mc-admin/mc-admin-user-svcacct-add.rst
          :start-after: start-mc-admin-svcacct-add-desc
          :end-before: end-mc-admin-svcacct-add-desc

   * - :mc:`~mc admin user svcacct disable`
     - .. include:: /reference/minio-mc-admin/mc-admin-user-svcacct-disable.rst
          :start-after: start-mc-admin-svcacct-disable-desc
          :end-before: end-mc-admin-svcacct-disable-desc

   * - :mc:`~mc admin user svcacct edit`
     - .. include:: /reference/minio-mc-admin/mc-admin-user-svcacct-edit.rst
          :start-after: start-mc-admin-svcacct-edit-desc
          :end-before: end-mc-admin-svcacct-edit-desc

   * - :mc:`~mc admin user svcacct enable`
     - .. include:: /reference/minio-mc-admin/mc-admin-user-svcacct-enable.rst
          :start-after: start-mc-admin-svcacct-enable-desc
          :end-before: end-mc-admin-svcacct-enable-desc

   * - :mc:`~mc admin user svcacct info`
     - .. include:: /reference/minio-mc-admin/mc-admin-user-svcacct-info.rst
          :start-after: start-mc-admin-svcacct-info-desc
          :end-before: end-mc-admin-svcacct-info-desc

   * - :mc:`~mc admin user svcacct list`
     - .. include:: /reference/minio-mc-admin/mc-admin-user-svcacct-list.rst
          :start-after: start-mc-admin-svcacct-list-desc
          :end-before: end-mc-admin-svcacct-list-desc

   * - :mc:`~mc admin user svcacct rm`
     - .. include:: /reference/minio-mc-admin/mc-admin-user-svcacct-remove.rst
          :start-after: start-mc-admin-svcacct-remove-desc
          :end-before: end-mc-admin-svcacct-remove-desc


.. toctree::
   :titlesonly:
   :hidden:

   /reference/minio-mc-admin/mc-admin-user-svcacct-add
   /reference/minio-mc-admin/mc-admin-user-svcacct-disable
   /reference/minio-mc-admin/mc-admin-user-svcacct-edit
   /reference/minio-mc-admin/mc-admin-user-svcacct-enable
   /reference/minio-mc-admin/mc-admin-user-svcacct-info
   /reference/minio-mc-admin/mc-admin-user-svcacct-list
   /reference/minio-mc-admin/mc-admin-user-svcacct-remove
