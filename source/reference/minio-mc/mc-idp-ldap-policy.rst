.. _minio-mc-idp-ldap-policy:

======================
``mc idp ldap policy``
======================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc idp ldap policy

.. versionadded:: RELEASE.2023-05-26T23-31-54Z

   :mc-cmd:`mc idp ldap policy` 及其子命令替代 ``mc admin idp ldap policy``。

说明
----

.. start-mc-idp-ldap-policy-desc

:mc-cmd:`mc idp ldap policy` 命令用于显示策略与关联组或用户之间的映射关系。

.. end-mc-idp-ldap-policy-desc

:mc-cmd:`mc idp ldap policy` 命令仅支持 MinIO 部署。

:mc-cmd:`mc idp ldap policy` 命令包含以下子命令：

.. list-table::
   :header-rows: 1
   :widths: 40 60

   * - 子命令
     - 说明

   * - :mc-cmd:`mc idp ldap policy attach`
     - .. include:: /reference/minio-mc/mc-idp-ldap-policy-attach.rst
          :start-after: start-mc-idp-ldap-policy-attach-desc
          :end-before: end-mc-idp-ldap-policy-attach-desc

   * - :mc-cmd:`mc idp ldap policy detach`
     - .. include:: /reference/minio-mc/mc-idp-ldap-policy-detach.rst
          :start-after: start-mc-idp-ldap-policy-detach-desc
          :end-before: end-mc-idp-ldap-policy-detach-desc

   * - :mc-cmd:`mc idp ldap policy entities`
     - .. include:: /reference/minio-mc/mc-idp-ldap-policy-entities.rst
          :start-after: start-mc-idp-ldap-policy-entities-desc
          :end-before: end-mc-idp-ldap-policy-entities-desc

.. toctree::
   :titlesonly:
   :hidden:

   /reference/minio-mc/mc-idp-ldap-policy-attach
   /reference/minio-mc/mc-idp-ldap-policy-detach
   /reference/minio-mc/mc-idp-ldap-policy-entities
