.. _minio-server-envvar-iam:

=======================================
身份与访问管理设置
=======================================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

本节页面记录了用于配置 MinIO 与身份与访问管理（IAM）方案协同工作的相关设置。
MinIO 支持的每种 IAM 方法都对应一个独立的设置页面。

- :ref:`Active Directory / LDAP <minio-server-envvar-external-identity-management-ad-ldap>`
- :ref:`OpenID <minio-server-envvar-external-identity-management-openid>`
- :ref:`MinIO Identity Management Plugin <minio-server-envvar-external-identity-management-plugin>`
- :ref:`MinIO Access Management Plugin <minio-server-envvar-external-identity-management-plugin>`

.. toctree::
   :titlesonly:
   :hidden:
   
   /reference/minio-server/settings/iam/ldap
   /reference/minio-server/settings/iam/openid
   /reference/minio-server/settings/iam/minio-identity-plugin
   /reference/minio-server/settings/iam/minio-access-plugin
