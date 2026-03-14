.. _minio-k8s-upgrade-minio-tenant:

======================
升级 MinIO Tenant
======================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 1


以下步骤用于升级单个 MinIO Tenant，可选择使用 Kustomize 或 Helm。
MinIO 建议你在升级生产 Tenant 之前，先在 Dev 或 QA 等较低环境中测试升级流程。

.. important::

   对于使用早于 :minio-release:`RELEASE.2024-03-30T09-41-56Z` 的 MinIO Image 且启用了 :ref:`AD/LDAP <minio-ldap-config-settings>` 的 Tenant，在开始本步骤前，你 **必须** 先完整阅读 :minio-release:`RELEASE.2024-04-18T19-09-19Z` 的发布说明。
   你必须将该发布说明中记录的额外步骤纳入升级过程。

.. _minio-upgrade-tenant-plugin:
.. _minio-upgrade-tenant-kustomize:

使用 Kustomize 升级 Tenant
--------------------------

以下步骤使用 Kustomize 和 ``kubectl`` CLI 升级 MinIO Tenant。
如果你是使用 :ref:`Helm <deploy-tenant-helm>` 部署 Tenant，请改用 :ref:`minio-upgrade-tenant-helm` 步骤。

若要使用 Kustomize 升级 Tenant：

如果 Tenant 是通过 Operator Console 部署的，则在升级前还需要额外步骤来创建基础配置文件。

如果 Tenant 是通过 Kustomize 部署的，则基础配置就是原始 Tenant 部署中已有的 ``kustomization`` 文件。

请根据 Tenant 的部署方式选择下方标签页：

.. tab-set::

   .. tab-item:: Operator Console-Deployed Tenant
      :selected:

      1. 创建基础配置文件：

         a. 在一个合适的目录中，使用 ``kubectl get`` 将当前 Tenant 配置保存到文件：

             .. code-block:: shell
                :class: copyable

                kubectl get tenant/my-tenant -n my-tenant-ns -o yaml > my-tenant-base.yaml

             将 ``my-tenant`` 和 ``my-tenant-ns`` 替换为待升级 Tenant 的名称和命名空间。

             编辑该文件，删除以下几行：

             - ``creationTimestamp:``
             - ``resourceVersion:``
             - ``uid:``
             - ``selfLink:``（如果存在）

             例如，删除高亮显示的这些行：

             .. code-block:: shell
                :emphasize-lines: 2, 6, 7

                metadata:
                  creationTimestamp: "2024-05-29T21:22:20Z"
                  generation: 1
                  name: my-tenant
                  namespace: my-tenant-ns
                  resourceVersion: "4699"
                  uid: d5b8e468-3bed-4aa3-8ddb-dfe1ee0362da

         b. 在同一目录中，创建一个 ``kustomization.yaml`` 文件，其内容类似如下：

            .. code-block:: shell
               :class: copyable

               apiVersion: kustomize.config.k8s.io/v1beta1
               kind: Kustomization

               resources:
               - my-tenant-base.yaml

               patches:
               - path: upgrade-minio-tenant.yaml

            如果你在上一步为 ``kubectl get`` 输出使用了不同的文件名，请将 ``my-tenant-base.yaml`` 替换为对应文件名。

   .. tab-item:: 现有 Kustomize 部署 Tenant

      1. 你可以使用原始部署中的 ``kustomization`` 文件作为基础配置来升级 Tenant。
         如果你已经没有这些文件，请按照 Operator Console-Deployed Tenant 标签页中的说明操作。

2. 创建一个 ``upgrade-minio-tenant.yaml`` 文件，其内容类似如下：

.. code-block:: shell
   :class: copyable
   :substitutions:

   apiVersion: minio.min.io/v2
   kind: Tenant

   metadata:
     name: my-tenant
     namespace: my-tenant-ns

   spec:
     image: minio/minio:|minio-tag|

该文件会指示 Kustomize 使用指定镜像升级 Tenant。
该文件名 ``upgrade-minio-tenant.yaml`` 必须与上一步创建的 ``kustomization.yaml`` 中 ``patches.path`` 指定的文件名一致。

将 ``my-tenant`` 和 ``my-tenant-ns`` 替换为待升级 Tenant 的名称和命名空间。
在 ``image:`` 中指定要升级到的 MinIO 版本。

或者，你也可以按照本地流程直接更新基础配置。
更多信息请参阅 :kube-docs:`Kustomize Documentation <tasks/manage-kubernetes-objects/kustomization>`。

3. 在与上述文件相同的目录中，使用 ``kubectl apply`` 将更新后的配置应用到 Tenant：

  .. code-block:: shell
     :class: copyable

     kubectl apply -k ./

  输出类似如下：

  .. code-block:: shell

     tenant.minio.min.io/my-tenant configured


.. _minio-upgrade-tenant-helm:

使用 MinIO Helm Chart 升级 Tenant
-----------------------------------

本步骤使用 Helm Charts 升级现有 MinIO Tenant。

如果你是通过 Kustomize 部署 Tenant，请改用 :ref:`minio-upgrade-tenant-kustomize` 步骤。

1. 验证现有 MinIO Tenant 安装。

   使用 ``kubectl get all -n TENANT_NAMESPACE`` 验证所有 Tenant pod 和 service 的健康状态。

   使用 ``helm list`` 命令查看该命名空间中已安装的 chart：

   .. code-block:: shell
      :class: copyable

      helm list -n TENANT_NAMESPACE

   结果应类似如下：

   .. code-block:: shell


      NAME            NAMESPACE         REVISION        UPDATED                                 STATUS          CHART           APP VERSION
      CHART_NAME      TENANT_NAMESPACE  1               2023-11-01 15:49:58.810412732 -0400 EDT deployed        tenant-5.0.x   v5.0.x

#. 更新 Operator 仓库

   使用 ``helm repo update minio-operator`` 更新 MinIO Operator 仓库。
   如果你为 MinIO Operator 仓库设置了不同的别名，请在命令中指定该别名。
   你可以使用 ``helm repo list`` 查看已安装的仓库列表。

   在更新 Operator 仓库后，使用 ``helm search`` 检查最新可用的 chart 版本：

   .. code-block:: shell
      :class: copyable

      helm search repo minio-operator

   返回结果应类似如下：

   .. code-block:: shell
      :class: copyable
      :substitutions:

      NAME                            CHART VERSION   APP VERSION     DESCRIPTION                    
      minio-operator/minio-operator   4.3.7           v4.3.7          A Helm chart for MinIO Operator
      minio-operator/operator         |operator-version-stable|          v|operator-version-stable|         A Helm chart for MinIO Operator
      minio-operator/tenant           |operator-version-stable|          v|operator-version-stable|         A Helm chart for MinIO Operator

   ``minio-operator/minio-operator`` 是旧版 chart，正常情况下**不应**安装。

#. 运行 ``helm upgrade``

   Helm 会使用最新 chart 升级 Tenant：

   .. code-block:: shell
      :class: copyable

      helm upgrade -n minio-tenant \
        CHART_NAME minio-operator/tenant

   命令结果应返回成功，并且 ``REVISION`` 值会递增。

#. 验证 Tenant 升级

   检查所有 service 和 pod 是否都已在线并正常工作。
