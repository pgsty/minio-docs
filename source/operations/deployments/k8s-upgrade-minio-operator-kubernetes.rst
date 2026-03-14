.. _minio-k8s-upgrade-minio-operator:

======================
升级 MinIO Operator
======================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 1

你可以随时升级 MinIO Operator，而不会影响其管理的 MinIO Tenant。

在升级过程中，Operator 可能会更新并重启 Tenant，以支持 MinIO 自定义资源定义（CRD）的变更。
这些变更不需要操作员或管理员采取额外操作，也不会影响 Tenant 的运行。

本页说明如何将 Operator 从 5.0.15 升级到 |operator-version-stable|。
如果在开始本流程前需要先升级到 Operator 5.0.15，请参阅 :ref:`minio-k8s-upgrade-minio-operator-to-5.0.15`。


.. admonition:: Operator 6.0.0 弃用 Operator Console

   从 Operator 6.0.0 开始，MinIO Operator Console 已被弃用并移除。

   你可以继续使用 Kustomize 或 Helm 等标准 Kubernetes 方式来管理和部署 MinIO Tenant。

.. _minio-k8s-upgrade-minio-operator-procedure:

将 MinIO Operator 从 5.0.15 升级到 |operator-version-stable|
------------------------------------------------------------------

.. important::

   Operator 6.0.0 弃用 MinIO Operator Console，并从 MinIO Operator CRD 中移除相关资源。
   其中包括 Operator Console 的 service、pod 等资源。

   后续请改用 Kustomize 或 Helm 来管理 Tenant。

.. tab-set::

   .. tab-item:: 使用 Kustomize 升级

      以下步骤使用 Kustomize 升级 MinIO Operator。
      对于使用 Operator 5.0.0 到 5.0.14 的部署，请先按照 :ref:`minio-k8s-upgrade-minio-operator-to-5.0.15` 完成升级，再执行本流程。

      如果你是通过 :ref:`Helm <minio-k8s-deploy-operator-helm>` 安装 Operator，请改用 :guilabel:`使用 Helm 升级` 步骤。

      .. container:: procedure

         #. *(可选)* 将每个 MinIO Tenant 升级到最新稳定版 MinIO。

            定期升级 MinIO 可确保 Tenant 获得最新特性和性能改进。
            在将升级应用到生产 Tenant 之前，请先在 Dev 或 QA Tenant 等较低环境中验证。
            升级 MinIO Tenant 的具体流程请参阅 :ref:`minio-k8s-upgrade-minio-tenant`。

         #. 验证现有 Operator 安装。
            使用 ``kubectl get all -n minio-operator`` 验证所有 Operator pod 和 service 的健康状态与运行状态。

            如果你将 Operator 安装到了自定义命名空间，请在命令中指定 ``-n <NAMESPACE>``。

            你可以通过获取该命名空间中某个 operator pod 的对象规范，确认当前安装的 Operator 版本。
            以下示例使用 ``jq`` 工具从 ``kubectl`` 输出中过滤出所需信息：

            .. code-block:: shell
               :class: copyable

               kubectl get pod -l 'name=minio-operator' -n minio-operator -o json | jq '.items[0].spec.containers'

            输出类似如下：

            .. code-block:: json
               :emphasize-lines: 8-10
               :substitutions:

               {
                  "env": [
                     {
                        "name": "CLUSTER_DOMAIN",
                        "value": "cluster.local"
                     }
                  ],
                  "image": "minio/operator:v5.0.15",
                  "imagePullPolicy": "IfNotPresent",
                  "name": "minio-operator"
               }

            如果本地主机未安装 ``jq``，你也可以只执行命令的前半部分，然后在输出中查找 ``spec.containers`` 段落。

         #. 使用 Kustomize 升级 Operator

            以下命令会将 Operator 升级到 |operator-version-stable|：

            .. code-block:: shell
               :class: copyable

               kubectl apply -k github.com/minio/operator

            在下面的示例输出中，``configured`` 表示更新后的 CRD 已应用对应变更：

            .. code-block:: shell

               namespace/minio-operator unchanged
               customresourcedefinition.apiextensions.k8s.io/miniojobs.job.min.io configured
               customresourcedefinition.apiextensions.k8s.io/policybindings.sts.min.io configured
               customresourcedefinition.apiextensions.k8s.io/tenants.minio.min.io configured
               serviceaccount/minio-operator unchanged
               clusterrole.rbac.authorization.k8s.io/minio-operator-role configured
               clusterrolebinding.rbac.authorization.k8s.io/minio-operator-binding unchanged
               service/operator unchanged
               service/sts unchanged
               deployment.apps/minio-operator configured

         #. 验证 Operator 升级结果

            你可以使用前面相同的 ``kubectl`` 命令检查新的 Operator 版本：

            .. code-block:: shell
               :class: copyable

               kubectl get pod -l 'name=minio-operator' -n minio-operator -o json | jq '.items[0].spec.containers'

   .. tab-item:: 使用 Helm 升级

      以下步骤使用 Helm 升级现有的 MinIO Operator 安装。

      如果你是使用 Kustomize 安装 Operator，请改用 :guilabel:`使用 Kustomize 升级` 步骤。

      .. container:: procedure

         #. *(可选)* 将每个 MinIO Tenant 升级到最新稳定版 MinIO。

            定期升级 MinIO 可确保 Tenant 获得最新特性和性能改进。
            在将升级应用到生产 Tenant 之前，请先在 Dev 或 QA Tenant 等较低环境中验证。
            升级 MinIO Tenant 的具体流程请参阅 :ref:`minio-k8s-upgrade-minio-tenant`。

         #. 验证现有 Operator 安装。

            使用 ``kubectl get all -n minio-operator`` 验证所有 Operator pod 和 service 的健康状态与运行状态。

            如果你将 Operator 安装到了自定义命名空间，请在命令中指定 ``-n <NAMESPACE>``。

            使用 ``helm list`` 查看该命名空间中已安装的 chart：

            .. code-block:: shell
               :class: copyable

               helm list -n minio-operator

            结果应类似如下：

            .. code-block:: shell
               :class: copyable

               NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
               operator        minio-operator  1               2023-11-01 15:49:54.539724775 -0400 EDT deployed        operator-5.0.x v5.0.x   

         #. 更新 Operator 仓库

            使用 ``helm repo update minio-operator`` 更新 MinIO Operator 仓库。
            如果你为 MinIO Operator 仓库设置了不同别名，请在命令中使用该别名替代 ``minio-operator``。
            你可以使用 ``helm repo list`` 查看当前已安装的仓库。

            更新 Operator 仓库后，使用 ``helm search`` 检查最新可用的 chart 版本：

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

            Helm 会使用最新 chart 升级 MinIO Operator：

            .. code-block:: shell
               :class: copyable

               helm upgrade -n minio-operator \
               operator minio-operator/operator

            如果你将 MinIO Operator 安装到了其他命名空间，请在 ``-n`` 参数中指定该命名空间。

            如果你使用的安装名不是 ``operator``，请将上面的值替换为实际安装名。

            命令应返回成功，并且 ``REVISION`` 值会递增。

         #. 验证 Operator 升级结果

            你可以使用前面相同的 ``kubectl`` 命令检查新的 Operator 版本：

            .. code-block:: shell
               :class: copyable

               kubectl get pod -l 'name=minio-operator' -n minio-operator -o json | jq '.items[0].spec.containers'
