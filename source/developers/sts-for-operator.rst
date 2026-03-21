.. _minio-sts-operator:

=========================================================
MinIO Operator 的 Security Token Service (STS)
=========================================================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

概述
----

.. versionadded:: Operator v5.0.0

   MinIO Operator 支持一组 API 调用，使应用程序能够为 MinIO Tenant 获取 STS 凭证。

MinIO Operator 的 STS 具有以下优势：

- :ref:`STS 凭证 <minio-security-token-service>` 允许应用程序访问 MinIO Tenant 上的对象，而无需在租户上为该应用创建凭证。

- 允许应用程序使用 Kubernetes 原生认证机制访问 MinIO 租户中的对象。
  
  Service Account 或 Service Account Token 是 Kubernetes 中 :kube-docs:`Role-Based Access Control (RBAC) <reference/access-authn-authz/rbac/>` :kube-docs:`authentication <reference/access-authn-authz/authentication/#service-account-tokens>` 的核心概念。

- 为 MinIO Operator 实现 STS 后，你可以通过租户 custom resource definition (CRD) 和 MinIO PolicyBinding CRD，利用基础设施即代码的原则与配置方式。

.. important:: 

   从 Operator v5.0.11 开始，STS 默认 *启用*。

   较早版本的 Operator 默认 *禁用* STS。
   若要在 v5.0.10 或更早版本的 Operator 中使用 STS，必须先显式启用。

   本页流程包含在 MinIO Operator 中启用 STS API 的说明。

Kubernetes 中 STS 授权的工作方式
--------------------------------

应用程序可以使用包含 :kube-docs:`Kubernetes Service Account <reference/access-authn-authz/service-accounts-admin/>` :abbr:`JWT (JSON Web Token)` 的 ``AssumeRoleWithWebIdentity`` 调用，向 MinIO Operator 请求临时凭证。
当该 Service Account 关联到 Pod 时（例如通过 deployment 的 ``.spec.spec.serviceAccountName`` 字段），Kubernetes 会从已知位置挂载该 Service Account 的 :abbr:`JWT (JSON Web Token)`，例如 ``/var/run/secrets/kubernetes.io/serviceaccount/token``。
Pod 可以从该位置访问这些 Service Account。

Operator 会检查请求有效性，检索该应用的策略，从租户获取凭证，然后将凭证返回给应用程序。
应用程序使用签发的凭证在该租户上执行对象存储操作。

.. image:: /images/k8s/sts-diagram.png
   :width: 600px
   :alt: 展示 Kubernetes MinIO 部署中 STS token 流程的示意图，涉及请求应用、MinIO Operator、Kubernetes API、PolicyBinding custom resource definition 以及 MinIO tenant。
   :align: center

完整流程包括以下步骤：

1. 应用程序向 MinIO Operator 发送 ``AssumeRoleWithWebidentity`` :ref:`API 请求 <minio-sts-assumerolewithwebidentity>`，其中包含租户命名空间和要使用的 Service Account。
2. MinIO Operator 使用 Kubernetes API 检查应用请求中与 :ref:`service account <minio-operator-sts-service-account>` 关联的 JSON Web Token (JWT) 是否有效。
3. Kubernetes API 返回其有效性检查结果。
4. MinIO Operator 检查与应用程序匹配的 :ref:`Policy Bindings <minio-operator-sts-policy-binding>`。
5. PolicyBinding CRD 返回与请求匹配的策略（如果有）。
6. MinIO Operator 将该应用程序的合并策略信息发送给 MinIO Tenant。
7. 租户为该请求创建与策略匹配的临时凭证，并将其返回给 MinIO Operator。
8. MinIO Operator 将临时凭证转发回应用程序。
9. 应用程序使用该凭证向 MinIO Tenant 发起对象存储调用。

要求
----

MinIO Operator 的 STS 需要满足以下条件：

- MinIO Operator v5.0.0 或更高版本。
- 部署 **必须** :ref:`配置 TLS <minio-tls>`。
- （Operator v5.0.0 - 5.0.10 必需）:envvar:`OPERATOR_STS_ENABLED` 环境变量设置为 ``on``。

步骤
----

1. 为部署启用 STS 功能

   .. note::

      对于 Operator 5.0.11 及更高版本，此步骤是可选的。
   
   .. code-block:: shell
      :class: copyable

      kubectl -n minio-operator set env deployment/minio-operator OPERATOR_STS_ENABLED=on
   
   - 将 ``minio-operator`` 替换为你的部署命名空间。
   - 将 ``deployment/minio-operator`` 替换为你的部署中 MinIO Operator 的值。

     你可以运行 ``kubectl get deployments -n <namespace>`` 查找 deployment 值，其中 ``<namespace>`` 需替换为 MinIO Operator 的命名空间。
     你的 MinIO Operator 命名空间通常是 ``minio-operator``，但该值在安装过程中可能发生变化。

2. 确保 MinIO Tenant 上存在适用于该应用程序的 :ref:`policy <minio-policy>`（一个或多个）

   下一步会使用 YAML 文档，通过名为 ``PolicyBinding`` 的 custom resource 将一个或多个现有租户策略映射到 Service Account。

3. 为 Service Account 和 Policy Binding 创建 YAML 资源：

   - 在 MinIO Tenant 中创建供应用程序使用的 :ref:`Service Account <minio-operator-sts-service-account>`。

     关于 Kubernetes 中 Service Account 的更多信息，请参见 :kube-docs:`Kubernetes 文档 <reference/access-authn-authz/service-accounts-admin/>`。
   - 在目标租户命名空间中创建 :ref:`Policy Binding <minio-operator-sts-policy-binding>`，将应用程序关联到 MinIO Tenant 的一个或多个策略。

4. 将 YAML 文件应用到部署以创建资源
   
   .. code-block:: shell
      :class: copyable

      kubectl apply -k path/to/yaml/file.yaml

5. 使用支持 ``AssumeRoleWithWebIdentity`` 类行为的 SDK，从你的应用程序向部署发送调用

   STS API 要求 Kubernetes 环境中存在该 Service Account 的 JWT。
   当该 Service Account 关联到 Pod 时（例如通过 deployment 的 ``.spec.spec.serviceAccountName`` 字段），Kubernetes 会从已知位置挂载该 Service Account 的 :abbr:`JWT (JSON Web Token)`，例如 ``/var/run/secrets/kubernetes.io/serviceaccount/token``。
   
   或者，你也可以将 token 路径定义为环境变量：

   .. code-block:: shell
      :class: copyable

      AWS_WEB_IDENTITY_TOKEN_FILE=/var/run/secrets/kubernetes.io/serviceaccount/token

   以下 MinIO SDK 支持 ``AssumeRoleRoleWithWebIdentity``：

   - :ref:`Golang <go-sdk>`
   - :ref:`Java <java-sdk>`
   - :ref:`JavaScript <javascript-sdk>`
   - :ref:`.NET <dotnet-sdk>`
   - :ref:`Python <python-sdk>`

   关于使用 SDK Assume role 的示例，请参见 :minio-git:`GitHub <operator/tree/master/examples/kustomization/sts-example/sample-clients>`。

示例资源
--------

.. _minio-operator-sts-service-account:

Service Account（服务账户）
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Service Account 是一种 :kube-docs:`Kubernetes 资源类型 <reference/access-authn-authz/service-accounts-admin/>`，允许外部应用程序与 Kubernetes 部署交互。
当该 Service Account 关联到 Pod 时（例如通过 deployment 的 ``.spec.spec.serviceAccountName`` 字段），Kubernetes 会从已知位置挂载该 Service Account 的 :abbr:`JWT (JSON Web Token)`，例如 ``/var/run/secrets/kubernetes.io/serviceaccount/token``。

以下 YAML 会在 ``sts-client`` 命名空间中创建名为 ``stsclient-sa`` 的 Service Account。

.. code-block:: yaml
   :class: copyable

   apiVersion: v1
   kind: ServiceAccount
   metadata:
     namespace: sts-client # The namespace to add the service account to. Usually a tenant, but can be any namespace in the deployment.
     name: stsclient-sa # The name to use for the service account.

.. _minio-operator-sts-policy-binding:

Policy Binding（策略绑定）
~~~~~~~~~~~~~~~~~~~~~~~~~~

``PolicyBinding`` 是 Kubernetes 中 MinIO 特有的 custom resource 类型，用于将 ``application`` 关联到一组策略。

在其所属租户的命名空间中创建 Policy Binding。

在 MinIO Operator 的语境下，application 是指任何使用特定 Service Account 和租户命名空间进行标识的请求资源。
``PolicyBinding`` 资源将该 application 关联到该命名空间中租户的一个或多个策略。

下面的 YAML 创建了一个 ``PolicyBinding``，将位于 ``sts-client`` 命名空间中、使用 Service Account ``stsclient-sa`` 的 application，关联到位于 ``minio-tenant-1`` 命名空间的目标租户中的策略 ``test-bucket-rw``。
YAML 定义中授予的策略 **必须** 已存在于 MinIO Tenant 上。

.. code-block:: yaml
   :class: copyable

   apiVersion: sts.min.io/v1alpha1
   kind: PolicyBinding
   metadata:
     name: binding-1
     namespace: minio-tenant-1 # The namespace of the tenant this binding is for
   spec:
     application:
       namespace: sts-client # The namespace that contains the service account for the application
       serviceaccount: stsclient-sa # The service account to use for the application
     policies:
       - test-bucket-rw # A policy that already exists in the tenant
       # - test-bucket-policy-2 # Add as many policies as needed

参考
----

- :minio-git:`按 SDK 分类的 STS 示例 <operator/tree/master/examples/kustomization/sts-example/sample-clients>`
- :kube-docs:`Kubernetes Service Account 文档 <reference/access-authn-authz/service-accounts-admin/>`
- :minio-git:`MinIO STS API <operator/blob/master/docs/policybinding_crd.adoc>`
