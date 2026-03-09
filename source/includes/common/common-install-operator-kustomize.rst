.. _minio-k8s-deploy-operator-kustomize-repo-2:

使用 Kustomize 安装 MinIO Operator
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下流程使用 ``kubectl -k`` 从 MinIO Operator GitHub 仓库安装 Operator。
``kubectl -k`` 和 ``kubectl --kustomize`` 是执行同一命令的别名。

.. important::

   如果你使用 Kustomize 安装 Operator，
   则必须继续使用 Kustomize 来管理或升级该安装。
   不要使用 ``kubectl krew``、Helm chart 或类似方法，
   去管理或升级通过 Kustomize 部署的 MinIO Operator。

   不过，你可以使用 Kustomize 升级先前通过 MinIO Kubernetes Plugin
   安装的旧版本 Operator（5.0.14 或更早版本）。

#. 安装最新版本的 Operator

   以下命令会将 Operator 安装到 ``minio-operator`` 命名空间：

   .. code-block:: shell
      :class: copyable
      :substitutions:

      kubectl apply -k "github.com/minio/operator?ref=v|operator-version-stable|"

   该命令会输出已安装资源列表。

#. 验证 Operator pod 是否正在运行：

   .. code-block:: shell
      :class: copyable

      kubectl get pods -n minio-operator

   输出类似如下：

   .. code-block:: shell

      NAME                              READY   STATUS              RESTARTS   AGE
      minio-operator-6c758b8c45-nkhlx   1/1     Running   0          2m42s
      minio-operator-6c758b8c45-dgd8n   1/1     Running   0          2m42s

   在这个示例中，``minio-operator`` pod 对应 MinIO Operator，
   ``console`` pod 对应 Operator Console。

   你可以通过应用 kubectl patch 来修改 Operator 部署。
   常见配置示例可在
   `Operator GitHub repository <https://github.com/minio/operator/tree/master/examples/kustomization>`__
   中找到。

   .. _minio-k8s-deploy-operator-access-console:

#. 验证 Operator 安装

   检查指定命名空间（``minio-operator``）中的内容，
   确认所有 pod 和 service 都已成功启动。

   .. code-block:: shell
      :class: copyable

      kubectl get all -n minio-operator

   返回结果应类似如下：

   .. code-block:: shell

      NAME                                  READY   STATUS    RESTARTS   AGE
      pod/minio-operator-6c758b8c45-nkhlx   1/1     Running   0          5m20s
      pod/minio-operator-6c758b8c45-dgd8n   1/1     Running   0          5m20s

      NAME               TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                         AGE
      service/operator   ClusterIP   10.43.135.241   <none>        4221/TCP                        5m20s
      service/sts        ClusterIP   10.43.117.251   <none>        4223/TCP                        5m20s

      NAME                             READY   UP-TO-DATE   AVAILABLE   AGE
      deployment.apps/minio-operator   2/2     2            2           5m20s

      NAME                                        DESIRED   CURRENT   READY   AGE
      replicaset.apps/minio-operator-6c758b8c45   2         2         2       5m20s

#. 后续步骤

   你可以使用 :ref:`MinIO CRD 和 Kustomize <minio-k8s-deploy-minio-tenant>`
   来部署 MinIO 租户。
   MinIO 也提供了 :ref:`用于部署 Tenant 的 Helm chart <deploy-tenant-helm>`。

   MinIO 建议对 Tenant 的部署和管理沿用安装 Operator 时所使用的同一种方法。
   在 Operator 或 Tenant 管理中混用 Kustomize 和 Helm
   可能会增加运维复杂度。
