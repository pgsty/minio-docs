.. _minio-upgrade:

======================
升级 MinIO 部署
======================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. important::

   对于早于 :minio-release:`RELEASE.2024-03-30T09-41-56Z` 且启用了 :ref:`AD/LDAP <minio-ldap-config-settings>` 的部署，在开始本步骤前，你 **必须** 先完整阅读 :minio-release:`RELEASE.2024-04-18T19-09-19Z` 的发布说明。
   你必须将该发布说明中记录的额外步骤纳入升级过程。

.. include:: /includes/linux/steps-upgrade-minio-deployment.rst
