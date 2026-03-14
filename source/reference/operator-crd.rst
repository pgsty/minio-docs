.. _minio-operator-crd:

================================
MinIO 自定义资源定义
================================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2


MinIO Operator 会安装一个 :kube-docs:`Custom Resource Definition (CRD) <concepts/extend-kubernetes/api-extension/custom-resources>`，用于描述 MinIO Tenant 对象。
Operator 使用该 CRD 在 Kubernetes 集群中对 Tenant 资源进行配置和管理。

本页提供 CRD 参考文档，用于自定义由 Operator 部署的 Tenant。
本文档默认读者已熟悉其中引用的 Kubernetes 概念、工具和流程。

.. include:: /includes/k8s/ext-tenant-crd.md
   :parser: myst_parser.sphinx_
