.. _minio-mc-idp-ldap-accesskey:

=========================
``mc idp ldap accesskey``
=========================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc idp ldap accesskey

.. versionadded:: RELEASE.2023-10-30T18-43-32Z

说明
----

.. start-mc-idp-ldap-accesskey-desc

:mc-cmd:`mc idp ldap accesskey` 命令可用于列出、删除或显示 LDAP 访问密钥对的信息。

.. end-mc-idp-ldap-accesskey-desc

:mc-cmd:`mc idp ldap accesskey` 命令仅支持 MinIO 部署。

.. include:: /includes/common-minio-ad-ldap-params.rst
   :start-after: start-minio-ad-ldap-accesskey-creation
   :end-before: end-minio-ad-ldap-accesskey-creation

:mc-cmd:`mc idp ldap accesskey` 命令包含以下子命令：

.. list-table::
   :header-rows: 1
   :widths: 40 60

   * - 子命令
     - 说明

   * - :mc-cmd:`mc idp ldap accesskey create`
     - .. include:: /reference/minio-mc/mc-idp-ldap-accesskey-create.rst
          :start-after: start-mc-idp-ldap-accesskey-create-desc
          :end-before: end-mc-idp-ldap-accesskey-create-desc

   * - :mc-cmd:`mc idp ldap accesskey disable`
     - .. include:: /reference/minio-mc/mc-idp-ldap-accesskey-disable.rst
          :start-after: start-mc-idp-ldap-accesskey-disable-desc
          :end-before: end-mc-idp-ldap-accesskey-disable-desc

   * - :mc-cmd:`mc idp ldap accesskey edit`
     - .. include:: /reference/minio-mc/mc-idp-ldap-accesskey-edit.rst
          :start-after: start-mc-idp-ldap-accesskey-edit-desc
          :end-before: end-mc-idp-ldap-accesskey-edit-desc

   * - :mc-cmd:`mc idp ldap accesskey enable`
     - .. include:: /reference/minio-mc/mc-idp-ldap-accesskey-enable.rst
          :start-after: start-mc-idp-ldap-accesskey-enable-desc
          :end-before: end-mc-idp-ldap-accesskey-enable-desc

   * - :mc-cmd:`mc idp ldap accesskey info`
     - .. include:: /reference/minio-mc/mc-idp-ldap-accesskey-info.rst
          :start-after: start-mc-idp-ldap-accesskey-info-desc
          :end-before: end-mc-idp-ldap-accesskey-info-desc

   * - :mc-cmd:`mc idp ldap accesskey ls`
     - .. include:: /reference/minio-mc/mc-idp-ldap-accesskey-ls.rst
          :start-after: start-mc-idp-ldap-accesskey-ls-desc
          :end-before: end-mc-idp-ldap-accesskey-ls-desc

   * - :mc-cmd:`mc idp ldap accesskey rm`
     - .. include:: /reference/minio-mc/mc-idp-ldap-accesskey-rm.rst
          :start-after: start-mc-idp-ldap-accesskey-rm-desc
          :end-before: end-mc-idp-ldap-accesskey-rm-desc

.. toctree::
   :titlesonly:
   :hidden:

   /reference/minio-mc/mc-idp-ldap-accesskey-disable
   /reference/minio-mc/mc-idp-ldap-accesskey-edit
   /reference/minio-mc/mc-idp-ldap-accesskey-enable
   /reference/minio-mc/mc-idp-ldap-accesskey-info
   /reference/minio-mc/mc-idp-ldap-accesskey-ls
   /reference/minio-mc/mc-idp-ldap-accesskey-rm
