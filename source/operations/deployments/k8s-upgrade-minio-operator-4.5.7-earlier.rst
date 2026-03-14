:orphan:

================================
升级旧版 MinIO Operator
================================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 1


MinIO 为旧版 MinIO Operator 支持以下升级路径：

.. list-table::
   :header-rows: 1
   :widths: 40 40
   :width: 100%

   * - 当前版本
     - 支持升级到

   * - 5.0.15 及更高版本
     - |operator-version-stable|

   * - 5.0.0 到 5.0.14
     - 5.0.15

   * - 4.2.3 到 4.5.7
     - 4.5.8
   
   * - 4.0.0 到 4.2.2
     - 4.2.3

   * - 3.X.X
     - 4.2.2

如果要从 4.5.7 或更早版本升级到 |operator-version-stable|，你必须先升级到 4.5.8，然后再升级到 5.0.15。
根据你当前的版本，可能需要经过一个或多个中间升级步骤才能到达 v4.5.8。

升级到 5.0.15 后，请参阅 :ref:`minio-k8s-upgrade-minio-operator` 继续升级到最新版本。

.. _minio-k8s-upgrade-minio-operator-to-5.0.15:

将 MinIO Operator 4.5.8 及更高版本升级到 5.0.15
------------------------------------------------

.. admonition:: 前提条件
   :class: note

   本流程需要满足以下条件：

   - 你已有一个运行 4.5.8 或更高版本的 MinIO Operator 部署
   - 你的 Kubernetes 集群版本为 1.21.0 或更高
   - 你的本地主机已安装 ``kubectl``，并已配置好对 Kubernetes 集群的访问

本流程将 MinIO Operator 从任意 4.5.8 及以上版本升级到 5.0.15

Tenant 自定义资源定义变更
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下变更适用于 Operator v5.0.0 或更高版本：

- ``.spec.s3`` 字段被 ``.spec.features`` 字段取代。
- ``.spec.credsSecret`` 字段被 ``.spec.configuration`` 字段取代。

  ``.spec.credsSecret`` 应保存 MinIO 部署中所有包含敏感信息的环境变量，这些变量不应出现在 ``.spec.env`` 中。
  该变更会影响 Tenant :abbr:`CRD (CustomResourceDefinition)`，且只影响直接编辑 tenant YAML 的用户，例如通过 Helm 或 Kustomize 管理的用户。
- **Log Search API**（``.spec.log``）和 **Prometheus**（``.spec.prometheus``）部署都已移除。
  不过，现有部署会保留为独立的 deployment 或 statefulset 继续运行，不再与 Tenant CR 关联。
  删除 Tenant :abbr:`CRD (Custom Resource Definition)` **不会** 级联删除日志或 Prometheus 部署。

  .. important::

     MinIO 建议你后续创建单独的 YAML 文件来管理这些部署。

Log Search 和 Prometheus
~~~~~~~~~~~~~~~~~~~~~~~~~

最新版本的 Operator 已将 Log Search 和 Prometheus 从内置工具中移除。
以下步骤会备份现有 YAML 文件、执行一些清理操作，并给出继续使用其中一个或两个功能的方法。

#. 备份 Prometheus 和 Log Search 的 YAML 文件。

   .. code-block:: shell
      :class: copyable

      export TENANT_NAME=myminio
      export NAMESPACE=mynamespace
      kubectl -n $NAMESPACE get secret $TENANT_NAME-log-secret -o yaml > $TENANT_NAME-log-secret.yaml
      kubectl -n $NAMESPACE get cm $TENANT_NAME-prometheus-config-map -o yaml > $TENANT_NAME-prometheus-config-map.yaml
      kubectl -n $NAMESPACE get sts $TENANT_NAME-prometheus -o yaml > $TENANT_NAME-prometheus.yaml
      kubectl -n $NAMESPACE get sts $TENANT_NAME-log -o yaml > $TENANT_NAME-log.yaml
      kubectl -n $NAMESPACE get deployment $TENANT_NAME-log-search-api -o yaml > $TENANT_NAME-log-search-api.yaml
      kubectl -n $NAMESPACE get svc $TENANT_NAME-log-hl-svc -o yaml > $TENANT_NAME-log-hl-svc.yaml
      kubectl -n $NAMESPACE get svc $TENANT_NAME-log-search-api -o yaml > $TENANT_NAME-log-search-api-svc.yaml
      kubectl -n $NAMESPACE get svc $TENANT_NAME-prometheus-hl-svc -o yaml > $TENANT_NAME-prometheus-hl-svc.yaml

   - 将 ``myminio`` 替换为正在升级的 operator 部署中对应 tenant 的名称。
   - 将 ``mynamespace`` 替换为正在升级的 operator 部署中该 tenant 所在的命名空间。

   对每个 tenant 重复执行。

#. 对所有 tenant 备份出的文件删除 ``.metadata.ownerReferences``。

#. *(可选)* 如果要继续使用 Log Search API 和 Prometheus，请在 tenant 的 YAML 规范文件中，将以下变量添加到 ``.spec.env`` 下。

   使用以下命令编辑 tenant：

   .. code-block:: shell
      :class: copyable

      kubectl edit tenants <TENANT-NAME> -n <TENANT-NAMESPACE>

   - 将 ``<TENANT-NAME>`` 替换为要修改的 tenant 名称。
   - 将 ``<TENANT-NAMESPACE>`` 替换为要修改的 tenant 所在命名空间。

   在文件的 ``.spec.env`` 下添加以下值：

   .. code-block:: yaml
      :class: copyable

      - name: MINIO_LOG_QUERY_AUTH_TOKEN
        valueFrom:
          secretKeyRef:
            key: MINIO_LOG_QUERY_AUTH_TOKEN
            name: <TENANT_NAME>-log-secret
      - name: MINIO_LOG_QUERY_URL
        value: http://<TENANT_NAME>-log-search-api:8080
      - name: MINIO_PROMETHEUS_JOB_ID
        value: minio-job
      - name: MINIO_PROMETHEUS_URL
        value: http://<TENANT_NAME>-prometheus-hl-svc:9001

   - 将 ``name`` 或 ``value`` 行中的 ``<TENANT_NAME>`` 替换为你的 tenant 名称。

操作步骤
~~~~~~~~~

.. tab-set::

   .. tab-item:: 使用 Kustomize 升级

      以下步骤使用 Kustomize 升级 MinIO Operator。

      对于通过 MinIO Kubernetes Plugin 安装的 Operator 5.0.1 到 5.0.14 版本，请按照下方 Kustomize 步骤先升级到 5.0.15 或更高版本。
      如果你是通过 :ref:`Helm <minio-k8s-deploy-operator-helm>` 安装 Operator，请改用 :guilabel:`使用 Helm 升级` 步骤。

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
               "image": "minio/operator:v5.0.x",
               "imagePullPolicy": "IfNotPresent",
               "name": "minio-operator"
            }

         如果本地主机未安装 ``jq``，你也可以只执行命令的前半部分，然后在输出中查找 ``spec.containers`` 段落。

      #. 使用 Kustomize 升级 Operator

         以下命令会将 Operator 升级到 5.0.15：

         .. code-block:: shell
            :class: copyable

            kubectl apply -k github.com/minio/operator/?ref=v5.0.15

         在下面的示例输出中，行尾的 ``configured`` 表示更新后的 CRD 已应用对应变更：

         .. code-block:: shell

            namespace/minio-operator configured
            customresourcedefinition.apiextensions.k8s.io/miniojobs.job.min.io configured
            customresourcedefinition.apiextensions.k8s.io/policybindings.sts.min.io configured
            customresourcedefinition.apiextensions.k8s.io/tenants.minio.min.io configured
            serviceaccount/console-sa unchanged
            serviceaccount/minio-operator unchanged
            clusterrole.rbac.authorization.k8s.io/console-sa-role unchanged
            clusterrole.rbac.authorization.k8s.io/minio-operator-role unchanged
            clusterrolebinding.rbac.authorization.k8s.io/console-sa-binding unchanged
            clusterrolebinding.rbac.authorization.k8s.io/minio-operator-binding unchanged
            configmap/console-env unchanged
            secret/console-sa-secret configured
            service/console unchanged
            service/operator unchanged
            service/sts unchanged
            deployment.apps/console configured
            deployment.apps/minio-operator configured


      #. 验证 Operator 升级结果

         你可以使用前面相同的 ``kubectl`` 命令检查新的 Operator 版本：

         .. code-block:: shell
            :class: copyable

            kubectl get pod -l 'name=minio-operator' -n minio-operator -o json | jq '.items[0].spec.containers'



   .. tab-item:: 使用 Helm 升级

      以下步骤使用 Helm 升级现有的 MinIO Operator 安装。

      如果你是使用 Kustomize 安装 Operator，请改用 :guilabel:`使用 Kustomize 升级` 步骤。

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

         你也可以直接查看 operator pod 以确认已安装版本。
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
               "image": "minio/operator:v5.0.x",
               "imagePullPolicy": "IfNotPresent",
               "name": "minio-operator"
            }

         如果本地主机未安装 ``jq``，你也可以只执行命令的前半部分，然后在输出中查找 ``spec.containers`` 段落。

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


.. _minio-k8s-upgrade-minio-operator-to-4.5.8:

将 MinIO Operator 4.2.3 到 4.5.7 升级到 4.5.8
---------------------------------------------------

前提条件
~~~~~~~~~~~~~

本流程需要满足以下条件：

- 你已有一个运行 4.2.3 到 4.5.7 的 MinIO Operator 部署
- 你的 Kubernetes 集群版本为 1.19.0 或更高
- 你的本地主机已安装 ``kubectl``，并已配置好对 Kubernetes 集群的访问

操作步骤
~~~~~~~~~

本流程会将 MinIO Operator 从 4.2.3 到 4.5.7 升级到 4.5.8。
随后你可以再从 4.5.8 升级到 5.0.15。

1. *(可选)* 将每个 MinIO Tenant 升级到最新稳定版 MinIO。

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

      {
         "env": [
            {
               "name": "CLUSTER_DOMAIN",
               "value": "cluster.local"
            }
         ],
         "image": "minio/operator:v4.5.1",
         "imagePullPolicy": "IfNotPresent",
         "name": "minio-operator"
      }

#. 下载最新稳定版 MinIO Kubernetes Plugin

   .. include:: /includes/k8s/install-minio-kubectl-plugin.rst

#. 运行初始化命令以升级 Operator

   使用 ``kubectl minio init`` 命令升级现有 MinIO Operator 安装：

   .. code-block:: shell
      :class: copyable

      kubectl minio init

#. 验证 Operator 升级结果

   你可以通过前一步中查看 Operator Pod 对象规范的方法，确认升级后的 Operator 版本。

.. _minio-k8s-upgrade-minio-operator-4.2.2-procedure:

将 MinIO Operator 4.0.0 到 4.2.2 升级到 4.2.3
---------------------------------------------------

前提条件
~~~~~~~~~~~~~

本流程假定满足以下条件：

- 你已有一个运行 4.0.0 到 4.2.2 任意版本的 MinIO Operator 部署
- 你的 Kubernetes 集群版本为 1.19.0 或更高
- 你的本地主机已安装 ``kubectl``，并已配置好对 Kubernetes 集群的访问

操作步骤
~~~~~~~~~

本流程涵盖将运行 4.0.0 到 4.2.2 任意版本的 MinIO Operator 部署升级到 4.2.3 所需的步骤。
随后你可以执行 :ref:`minio-k8s-upgrade-minio-operator-procedure`，完成升级到 |operator-version-stable|。

从 4.0.0 到 4.2.2 的安装无法直接升级到 |operator-version-stable|。

1. *(可选)* 将每个 MinIO Tenant 升级到最新稳定版 MinIO。

   定期升级 MinIO 可确保 Tenant 获得最新特性和性能改进。
   在将升级应用到生产 Tenant 之前，请先在 Dev 或 QA Tenant 等较低环境中验证。

   升级 MinIO Tenant 的具体流程请参阅 :ref:`minio-k8s-upgrade-minio-tenant`。

#. 检查每个 Tenant Pool 的 Security Context

   使用以下命令检查每个受管 MinIO Tenant 的规范：

   .. code-block:: shell
      :class: copyable

      kubectl get tenants <TENANT-NAME> -n <TENANT-NAMESPACE> -o yaml
   
   如果某个 Tenant 不存在 ``spec.pools.securityContext`` 字段，则该 tenant pod 很可能以 root 身份运行。
   
   从 4.2.3 及后续版本开始，作为 Operator 升级的一部分，pod 将使用受限权限集运行。
   但对于以 root 身份运行 pod 的 Tenant，可能会因为 security context 不匹配而启动失败。
   你可以为这些 Tenant 显式设置允许 pod 以 root 身份运行的 Security Context：

   .. code-block:: yaml
      :class: copyable

      securityContext:
        runAsUser: 0
        runAsGroup: 0
        runAsNonRoot: false
        fsGroup: 0

   你可以使用以下命令编辑 tenant 并应用变更：

   .. code-block:: shell

      kubectl edit tenants <TENANT-NAME> -n <TENANT-NAMESPACE>
      # 按需修改 securityContext

   更多有关 Kubernetes Security Context 的信息，请参阅 :kube-docs:`Pod Security Standards <concepts/security/pod-security-standards/>`。

#. 升级到 Operator 4.2.3

   下载 MinIO Kubernetes Plugin 4.2.3，并使用它升级 Operator。
   在浏览器中打开 https://github.com/minio/operator/releases/tag/v4.2.3，下载与你本地主机操作系统匹配的二进制文件。

   例如，使用 Intel 或 AMD 处理器的 Linux 主机可以运行以下命令：

   .. code-block:: shell
      :class: copyable

      wget https://github.com/minio/operator/releases/download/v4.2.3/kubectl-minio_4.2.3_linux_amd64 -o kubectl-minio_4.2.3
      chmod +x kubectl-minio_4.2.3
      ./kubectl-minio_4.2.3 init

#. 验证所有 Tenant 和 Operator pod

   检查 Operator 和 MinIO Tenant 命名空间，确保所有 pod 和 service 都已成功启动。

   例如：

   .. code-block:: shell
      :class: copyable

      kubectl get all -n minio-operator
      kubectl get pods -l "v1.min.io/tenant" --all-namespaces

#. 升级到 |operator-version-stable|

   按照 :ref:`minio-k8s-upgrade-minio-operator-procedure` 中的流程，升级到最新稳定版 Operator。

将 MinIO Operator 3.0.0 到 3.0.29 升级到 4.2.2
----------------------------------------------------

前提条件
~~~~~~~~~~~~~

本流程假定满足以下条件：

- 你已有一个运行 3.X.X 的 MinIO Operator 部署
- 你的 Kubernetes 集群版本为 1.19.0 或更高
- 你的本地主机已安装 ``kubectl``，并已配置好对 Kubernetes 集群的访问

操作步骤
~~~~~~~~~

本流程涵盖将运行 3.0.0 到 3.2.9 任意版本的 MinIO Operator 部署升级到 4.2.2 所需的步骤。
随后你可以执行 :ref:`minio-k8s-upgrade-minio-operator-4.2.2-procedure`，再执行 :ref:`minio-k8s-upgrade-minio-operator-procedure`。

3.X.X 系列安装无法直接升级到 |operator-version-stable|。

1. *(可选)* 将每个 MinIO Tenant 升级到最新稳定版 MinIO。

   定期升级 MinIO 可确保 Tenant 获得最新特性和性能改进。

   在将升级应用到生产 Tenant 之前，请先在 Dev 或 QA Tenant 等较低环境中验证。

   升级 MinIO Tenant 的具体流程请参阅 :ref:`minio-k8s-upgrade-minio-tenant`。

#. 验证 Tenant ``tenant.spec.zones`` 的值

   使用以下命令检查每个受管 MinIO Tenant 的规范：

   .. code-block:: shell
      :class: copyable

      kubectl get tenants <TENANT-NAME> -n <TENANT-NAMESPACE> -o yaml

   - 确保每个 ``tenant.spec.zones`` 元素都设置了 ``name`` 字段，且值为该 zone 的名称。
     同一个 Tenant 中每个 zone 的名称都必须唯一，例如第一个和第二个 zone 分别使用 ``zone-0`` 与 ``zone-1``。

   - 确保每个 ``tenant.spec.zones`` 都显式设置了 ``securityContext``，以描述 pod 在集群中运行时使用的权限集。

   以下 Tenant YAML 片段设置了这些字段：

   .. code-block:: yaml
      
      image: "minio/minio:$(LATEST-VERSION)"
      ...
      zones:
      - servers: 4
        name: "zone-0"
        volumesPerServer: 4
        volumeClaimTemplate:
           metadata:
           name: data
           spec:
           accessModes:
              - ReadWriteOnce
           resources:
              requests:
                 storage: 1Ti
        securityContext:
           runAsUser: 0
           runAsGroup: 0
           runAsNonRoot: false
           fsGroup: 0
      - servers: 4
        name: "zone-1"
        volumesPerServer: 4
        volumeClaimTemplate:
           metadata:
           name: data
           spec:
           accessModes:
              - ReadWriteOnce
           resources:
              requests:
                 storage: 1Ti
        securityContext:
           runAsUser: 0
           runAsGroup: 0
           runAsNonRoot: false
           fsGroup: 0

   你可以使用以下命令编辑 tenant 并应用变更：

   .. code-block:: shell

      kubectl edit tenants <TENANT-NAME> -n <TENANT-NAMESPACE>

#. 升级到 Operator 4.2.2

   下载 MinIO Kubernetes Plugin 4.2.2，并使用它升级 Operator。
   在浏览器中打开 https://github.com/minio/operator/releases/tag/v4.2.2，下载与你本地主机操作系统匹配的二进制文件。
   例如，使用 Intel 或 AMD 处理器的 Linux 主机可以运行以下命令：

   .. code-block:: shell
      :class: copyable

      wget https://github.com/minio/operator/releases/download/v4.2.3/kubectl-minio_4.2.2_linux_amd64 -o kubectl-minio_4.2.2
      chmod +x kubectl-minio_4.2.2

      ./kubectl-minio_4.2.2 init

#. 验证所有 Tenant 和 Operator pod

   检查 Operator 和 MinIO Tenant 命名空间，确保所有 pod 和 service 都已成功启动。

   例如：

   .. code-block:: shell
      :class: copyable

      kubectl get all -n minio-operator

      kubectl get pods -l "v1.min.io/tenant" --all-namespaces

#. 升级到 4.2.3

   按照 :ref:`minio-k8s-upgrade-minio-operator-4.2.2-procedure` 中的流程升级到 Operator 4.2.3。
   随后你可以继续升级到 |operator-version-stable|。
