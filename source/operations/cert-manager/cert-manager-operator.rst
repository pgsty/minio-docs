.. _minio-certmanager-operator:

========================================
使用 cert-manager 管理 Operator 证书
========================================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 1


MinIO Operator 负责管理 ``minio-operator`` 命名空间中各服务的 TLS 证书签发。

本页说明如何使用 :ref:`cert-manager <minio-certmanager>` 管理 Operator 的 TLS 证书。

前提条件
--------

- 使用受支持版本的 `Kubernetes <https://kubernetes.io/releases/>`__
- 已安装 `kustomize <https://kustomize.io/>`__
- 可以通过 ``kubectl`` 访问你的 ``k8s`` 集群
- 已完成 :ref:`cert-manager 配置 <minio-setup-certmanager>`
- MinIO Operator 尚未安装


1) 为 ``minio-operator`` 命名空间创建 CA Issuer
-----------------------------------------------

本指南将 **禁用** MinIO Operator 的自动证书生成功能，改为使用 cert-manager 签发证书。

``minio-operator`` 命名空间必须拥有自己的证书颁发机构（CA），该 CA 派生自你在 :ref:`配置 cert-manager <minio-certmanager>` 时创建的集群 ``ClusterIssuer`` 证书。
请使用 cert-manager 创建此 CA 证书。

.. important::

   该 CA 证书 **必须** 在安装 MinIO Operator *之前* 已存在。

1. 如果 ``minio-operator`` 命名空间尚不存在，请先创建：

   .. code-block:: shell
      :class: copyable

      kubectl create ns minio-operator

2. 申请一个新的 ``Certificate``，并设置 ``spec.isCA: true``。

   该证书将作为 ``minio-operator`` 命名空间的 :abbr:`CA (Certificate Authority)` 使用。

   创建一个名为 ``operator-ca-tls-secret.yaml`` 的文件，内容如下：

   .. code-block:: yaml
      :class: copyable
      :emphasize-lines: 7,8

      # operator-ca-tls-secret.yaml
      apiVersion: cert-manager.io/v1
      kind: Certificate
      metadata:
        name: minio-operator-ca-certificate
        namespace: minio-operator
      spec:
        isCA: true
        commonName: operator
        secretName: operator-ca-tls
        duration: 70128h # 8y
        privateKey:
          algorithm: ECDSA
          size: 256
        issuerRef:
          name: selfsigned-root
          kind: ClusterIssuer
          group: cert-manager.io

   .. important::

      ``spec.issueRef.name`` 必须与 :ref:`配置 cert-manager <minio-cert-manager-create-cluster-issuer>` 时创建的 ``ClusterIssuer`` 名称一致。
      如果你使用了不同的 ``ClusterIssuer`` 名称，或者使用了与本指南不同的 ``Issuer``，请根据你的环境调整 ``issuerRef``。

3. 应用该资源：
   
   .. code-block:: shell
      :class: copyable

      kubectl apply -f operator-ca-tls-secret.yaml

Kubernetes 会在 ``minio-operator`` 命名空间中创建一个名为 ``operator-ca-tls`` 的新 Secret。

.. important::

   任何需要与 MinIO Operator 交互的应用都必须信任此证书。


2) 使用该 Secret 创建 ``Issuer``
------------------------------------------------

使用 ``operator-ca-tls`` Secret 为 ``minio-operator`` 命名空间创建一个 ``Issuer`` 资源。

1. 创建一个名为 ``operator-ca-issuer.yaml`` 的文件，内容如下：

   .. code-block:: yaml

      # operator-ca-issuer.yaml
      apiVersion: cert-manager.io/v1
      kind: Issuer
      metadata:
        name: minio-operator-ca-issuer
        namespace: minio-operator
      spec:
        ca:
          secretName: operator-ca-tls


2. 应用该资源：
   
   .. code-block:: shell

      kubectl apply -f operator-ca-issuer.yaml

3) 创建 TLS 证书
----------------

现在 ``Issuer`` 已存在于 ``minio-operator`` 命名空间中，cert-manager 可以开始签发证书。

cert-manager 签发的证书必须对以下 DNS 名称有效：

- ``sts``
- ``sts.minio-operator.svc.``
- ``sts.minio-operator.svc.<cluster domain>``

  .. important::

      将 ``<cluster domain>`` 替换为你的环境实际使用的值。
      ``cluster domain`` 是 Kubernetes 集群内部的根 DNS 域。
      该值通常为 ``cluster.local``，但你仍应检查 CoreDNS 配置，以确认 Kubernetes 集群实际使用的值。
      
      例如：

      .. code-block:: shell
         :class: copyable

         kubectl get configmap coredns -n kube-system -o jsonpath="{.data}"

      不同 Kubernetes 发行版或托管平台对根域名的管理方式可能不同。
      更多信息请参阅你的 Kubernetes 提供方文档。

1. 为上述 DNS 名称创建一个 ``Certificate``：

   创建一个名为 ``sts-tls-certificate.yaml`` 的文件，内容如下：

   .. code-block:: yaml
      :class: copyable
      :emphasize-lines: 7,12

      # sts-tls-certificate.yaml
      apiVersion: cert-manager.io/v1
      kind: Certificate
      metadata:
        name: sts-certmanager-cert
        namespace: minio-operator
      spec:
        dnsNames:
          - sts
          - sts.minio-operator.svc
          - sts.minio-operator.svc.cluster.local # Replace cluster.local with the value for your domain.
        secretName: sts-tls
        issuerRef:
          name: minio-operator-ca-issuer

   .. important::
   
      ``spec.secretName`` 不是可选项。

      Secret 名称 **必须** 为 ``sts-tls``。
      请确认在证书 YAML 中按照高亮所示设置 ``spec.secretName: sts-tls``。

2. 应用该资源：

   .. code-block:: shell
      :class: copyable

      kubectl apply -f sts-tls-certificate.yaml

这会在 ``minio-operator`` 命名空间中创建一个名为 ``sts-tls`` 的 Secret。

.. warning::
  
   如果包含 TLS 证书的 ``sts-tls`` Secret 缺失，或者其中包含无效的 ``key-value`` 对，则 STS service 无法启动。

4) 在禁用 Auto TLS 的情况下安装 Operator
----------------------------------------

现在你可以 :ref:`安装 MinIO Operator <minio-operator-installation>`。

安装 Operator 时，请在 ``minio-operator`` 容器中将环境变量 ``OPERATOR_STS_AUTO_TLS_ENABLED`` 设置为 ``off``。

禁用该环境变量后，MinIO Operator 将不再自行签发证书。
此时 Operator 将依赖 cert-manager 签发 TLS 证书。

环境变量的具体定义方式取决于你的 Operator 安装方式。
以下步骤演示如何使用 kustomize 配置该变量。

1. 创建一个名为 ``kustomization.yaml`` 的 kustomization patch 文件，内容如下：

   .. code-block:: yaml
      :class: copyable
   
      # minio-operator/kustomization.yaml
      apiVersion: kustomize.config.k8s.io/v1beta1
      kind: Kustomization
      
      resources:
      - github.com/minio/operator/resources
      
      patches:
      - patch: |-
          apiVersion: apps/v1
          kind: Deployment
          metadata:
            name: minio-operator
            namespace: minio-operator
          spec:
            template:
              spec:
                containers:
                  - name: minio-operator
                    env:
                      - name: OPERATOR_STS_AUTO_TLS_ENABLED
                        value: "off"
                      - name: OPERATOR_STS_ENABLED
                        value: "on"

2. 将该 kustomization 资源应用到集群：

   .. code-block:: shell
      :class: copyable
   
      kubectl apply -k minio-operator

将现有 MinIO Operator 部署迁移到 cert-manager
---------------------------------------------

如果要将现有 MinIO Operator 部署从 AutoCert 迁移到 cert-manager，请完成以下步骤：

1. 完成 :ref:`安装 cert-manager <minio-certmanager>` 的步骤，并禁用 auto-cert。
2. 完成本页步骤 1 到 3，为 Operator 创建证书颁发机构。
3. 执行本页中的安装步骤时，不再使用原有的 Operator TLS 证书，而改用 cert-manager 签发的证书。
4. 参照 :ref:`用于租户的 cert-manager <minio-certmanager-tenants>` 页面，为每个租户创建新的 cert-manager 证书。
5. 使用每个租户对应的 cert-manager 证书 Secret，替换 MinIO Operator 命名空间中租户正在使用的 Secret。

后续步骤
--------

继续配置 :ref:`用于 MinIO 租户的 cert-manager <minio-certmanager-tenants>`。
