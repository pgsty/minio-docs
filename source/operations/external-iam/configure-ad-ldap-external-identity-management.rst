.. _minio-authenticate-using-ad-ldap-generic:

================================================
配置 MinIO 使用 Active Directory / LDAP 进行认证
================================================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2


概览
----

MinIO 支持配置单个 Active Directory / LDAP 连接，用于对用户身份进行外部管理。

本页流程提供以下内容的操作说明：

.. tab-set::
   :class: parent-tab

   .. tab-item:: Kubernetes
      :sync: k8s

      对于使用 :ref:`MinIO Kubernetes Operator <minio-kubernetes>` 部署的 MinIO Tenant，本流程包括：

      - 配置 MinIO Tenant 使用外部 AD/LDAP 提供方
      - 使用 AD/LDAP 凭证访问 Tenant Console
      - 使用 MinIO ``AssumeRoleWithLDAPIdentity`` Security Token Service (STS) API 生成供应用程序使用的临时凭证

   .. tab-item:: Baremetal
      :sync: baremetal

      对于部署在裸金属基础设施上的 MinIO，本流程包括：

      - 为 MinIO 集群配置外部 AD/LDAP 提供方
      - 使用 AD/LDAP 凭证访问 MinIO Console
      - 使用 MinIO ``AssumeRoleWithLDAPIdentity`` Security Token Service (STS) API 生成供应用程序使用的临时凭证

本流程是面向 AD/LDAP 服务的通用配置流程。
有关用户身份配置的具体步骤，请参阅你所选 AD/LDAP 提供方的文档。

前提条件
--------

访问 MinIO 集群
~~~~~~~~~~~~~~~

.. tab-set::
   

   .. tab-item:: Kubernetes
      :sync: k8s

      你必须能够访问 MinIO Operator Console Web UI。
      你可以使用自己偏好的 Kubernetes 路由组件暴露 MinIO Operator Console service，也可以通过临时端口转发，在本地机器上暴露 Console service 端口。

   .. tab-item:: Baremetal
      :sync: baremetal

      本流程使用 :mc:`mc` 对 MinIO 集群执行操作。
      请在一台能够访问该集群网络的机器上安装 ``mc``。
      关于如何下载和安装 ``mc``，请参见 ``mc`` :ref:`安装快速开始 <mc-install>`。

      本流程假定已为 MinIO 集群配置 :mc:`alias <mc alias>`。

兼容 Active Directory / LDAP 的身份提供方
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

本流程假定你已经拥有一个现成的 Active Directory 或 LDAP 服务。
AD/LDAP 本身的配置不在本流程范围内。

.. tab-set::
   

   .. tab-item:: Kubernetes
      :sync: k8s

      - 如果 AD/LDAP 部署与 MinIO Tenant 位于同一 Kubernetes 集群内，你可以使用 Kubernetes service 名称，让 MinIO Tenant 能够连接到 AD/LDAP 服务。

      - 如果 AD/LDAP 部署位于 Kubernetes 集群外部，则必须确保该集群支持 Kubernetes services、pods 与外部网络之间的通信路由。
        这可能需要配置或部署额外的 Kubernetes 网络组件，和/或启用访问公网的能力。

   .. tab-item:: Baremetal
      :sync: baremetal

      MinIO 部署必须与目标 AD / LDAP 服务保持双向网络连通。

MinIO 需要一组只读访问凭证，以便通过 :ref:`bind <minio-external-identity-management-ad-ldap-lookup-bind>` 执行认证用户和组查询。
请确保每个打算与 MinIO 配合使用的 AD/LDAP 用户和组，都在 MinIO 部署上拥有对应的 :ref:`策略 <minio-external-identity-management-ad-ldap-access-control>`。
如果某个 AD/LDAP 用户没有分配任何策略，*并且* 所属组也都没有分配任何策略，那么该用户无权访问 MinIO 集群中的任何操作或资源。

.. _minio-external-identity-management-ad-ldap-configure:

为 MinIO 配置 Active Directory 或 LDAP 外部身份管理
----------------------------------------------------------------------

.. include:: /includes/baremetal/steps-configure-ad-ldap-external-identity-management.rst

禁用已配置的 Active Directory / LDAP 连接
--------------------------------------------------------------

.. versionadded:: RELEASE.2023-03-20T20-16-18Z

你可以按需启用或禁用已配置的 AD/LDAP 连接。

使用 :mc:`mc idp ldap disable` 停用已配置连接。
使用 :mc:`mc idp ldap enable` 激活此前已配置的连接。
