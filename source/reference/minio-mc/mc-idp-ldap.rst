.. _minio-mc-idp-ldap:

===============
``mc idp ldap``
===============

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc idp ldap

.. versionadded:: RELEASE.2023-05-26T23-31-54Z

   :mc-cmd:`mc idp ldap` and its subcommands replace ``mc admin idp ldap``.

描述
----

.. start-mc-idp-ldap-desc

:mc-cmd:`mc idp ldap` 命令用于管理第三方 :ref:`Active Directory 或 LDAP 身份与访问管理（IAM）集成 <minio-external-identity-management-ad-ldap>` 的配置。

.. end-mc-idp-ldap-desc

:mc-cmd:`mc idp ldap` 命令可作为在 :ref:`设置 AD/LDAP 连接 <minio-authenticate-using-ad-ldap-generic>` 时使用环境变量的替代方案。它们仅支持 MinIO 部署。

有关如何使用这些命令的教程，请参阅 :ref:`minio-external-identity-management-ad-ldap`。

.. note::

   MinIO :ref:`AD/LDAP 环境变量 <minio-server-envvar-external-identity-management-ad-ldap>` 会覆盖通过此命令修改或设置的对应配置项。

:mc-cmd:`mc idp ldap` 命令包含以下子命令：

.. list-table::
   :header-rows: 1
   :widths: 40 60

   * - 子命令
     - 描述

   * - :mc-cmd:`mc idp ldap add`
     - .. include:: /reference/minio-mc/mc-idp-ldap-add.rst
          :start-after: start-mc-idp-ldap-add-desc
          :end-before: end-mc-idp-ldap-add-desc

   * - :mc-cmd:`mc idp ldap disable`
     - .. include:: /reference/minio-mc/mc-idp-ldap-disable.rst
          :start-after: start-mc-idp-ldap-disable-desc
          :end-before: end-mc-idp-ldap-disable-desc

   * - :mc-cmd:`mc idp ldap enable`
     - .. include:: /reference/minio-mc/mc-idp-ldap-enable.rst
          :start-after: start-mc-idp-ldap-enable-desc
          :end-before: end-mc-idp-ldap-enable-desc

   * - :mc-cmd:`mc idp ldap info`
     - .. include:: /reference/minio-mc/mc-idp-ldap-info.rst
          :start-after: start-mc-idp-ldap-info-desc
          :end-before: end-mc-idp-ldap-info-desc

   * - :mc-cmd:`mc idp ldap ls`
     - .. include:: /reference/minio-mc/mc-idp-ldap-ls.rst
          :start-after: start-mc-idp-ldap-ls-desc
          :end-before: end-mc-idp-ldap-ls-desc

   * - :mc-cmd:`mc idp ldap policy` 子命令
     - .. include:: /reference/minio-mc/mc-idp-ldap-policy.rst
          :start-after: start-mc-idp-ldap-policy-desc
          :end-before: end-mc-idp-ldap-policy-desc

   * - :mc-cmd:`mc idp ldap rm`
     - .. include:: /reference/minio-mc/mc-idp-ldap-rm.rst
          :start-after: start-mc-idp-ldap-rm-desc
          :end-before: end-mc-idp-ldap-rm-desc

   * - :mc-cmd:`mc idp ldap update`
     - .. include:: /reference/minio-mc/mc-idp-ldap-update.rst
          :start-after: start-mc-idp-ldap-update-desc
          :end-before: end-mc-idp-ldap-update-desc


.. toctree::
   :titlesonly:
   :hidden:

   /reference/minio-mc/mc-idp-ldap-add
   /reference/minio-mc/mc-idp-ldap-disable
   /reference/minio-mc/mc-idp-ldap-enable
   /reference/minio-mc/mc-idp-ldap-info
   /reference/minio-mc/mc-idp-ldap-ls
   /reference/minio-mc/mc-idp-ldap-rm
   /reference/minio-mc/mc-idp-ldap-update
