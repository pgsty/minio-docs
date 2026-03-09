.. _minio-security-token-service:

============================
Security Token Service (STS)
============================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

MinIO Security Token Service (STS) APIs 允许应用程序生成用于访问 MinIO 部署的临时凭证。

对于配置为使用外部身份管理器的 MinIO 部署，STS API 是*必需*的，因为该 API 可将外部 IDP 凭证转换为兼容 AWS Signature v4 的凭证。

STS API 端点
------------

MinIO 支持以下 STS API 端点：

.. list-table::
   :header-rows: 1
   :widths: 30 30 40

   * - 端点
     - 支持的 IDP
     - 说明

   * - :ref:`AssumeRoleWithWebIdentity <minio-sts-assumerolewithwebidentity>`
     - OpenID Connect
     - 使用 OIDC 提供方返回的 JWT token 生成 access key 和 secret key

   * - :ref:`AssumeRoleWithLDAPIdentity <minio-sts-assumerolewithldapidentity>`
     - Active Directory / LDAP
     - 使用为该 API 端点指定的 AD/LDAP 凭证生成 access key 和 secret key。

   * - :ref:`AssumeRoleWithCustomToken <minio-sts-assumerolewithcustomtoken>`
     - MinIO Identity Plugin
     - 生成一个 token，用于外部身份提供方和 :ref:`MinIO Identity Plugin <minio-external-identity-management-plugin>`。
 

.. toctree::
   :titlesonly:
   :hidden:
   :glob:

   /developers/security-token-service/*
   /developers/sts-for-operator
