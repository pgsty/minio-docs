.. _deploy-minio-tenant-redhat-openshift:

使用 OpenShift Web Console 部署租户
-----------------------------------

1) 访问 MinIO Operator 界面
~~~~~~~~~~~~~~~~~~~~~~~~~~~

你可以在左侧导航栏的 :guilabel:`Operators` 下找到 MinIO Operator 界面。

1. 进入 :guilabel:`Operators`，然后选择 :guilabel:`Installed Operators`。

2. 在 :guilabel:`Project` 下拉框中选择 :guilabel:`openshift-operators`。

3. 在已安装 Operator 列表中选择 :guilabel:`MinIO Operators`。

点击 :guilabel:`Create Tenant` 开始创建租户。

2) 创建租户
~~~~~~~~~~~
:guilabel:`Form View` 提供了用于配置新 MinIO 租户的用户界面。

.. image:: /images/openshift/minio-openshift-tenant-create-ui.png
   :align: center
   :width: 90%
   :class: no-scaled-link
   :alt: OpenShift 租户创建 UI 视图

- 确认 :guilabel:`Tenant Secret -> Name` 已设置为前提条件步骤中创建的
  MinIO Root User Kubernetes Secret 名称。

- 确认 :guilabel:`Console -> Console Secret -> Name` 已设置为前提条件步骤中创建的
  MinIO Console Kubernetes Secret 名称。

你也可以使用 YAML 视图对 MinIO 租户进行更细粒度的配置。
有关特定字段的设置指导，请参阅
:minio-git:`MinIO Custom Resource Definition Documentation <operator/blob/master/docs/tenant_crd.adoc>`。
MinIO 还提供了一些示例，可作为创建自定义租户 YAML 对象的补充参考。
请注意，OperatorHub 的 YAML 视图只支持创建 MinIO 租户对象。
不要在 YAML 输入中指定任何其他对象。

.. image:: /images/openshift/minio-openshift-tenant-create-yaml.png
   :align: center
   :width: 90%
   :class: no-scaled-link
   :alt: OpenShift 租户创建 UI 视图

其中一个视图中的更改会反映到另一个视图。
例如，你可以在 :guilabel:`YAML View` 中修改，
并在 :guilabel:`Form View` 中看到这些更改。

.. admonition:: 安全上下文配置
   :class: note

   如果你的 OpenShift 集群 Security Context 配置限制了受支持的 Pod
   安全上下文，请打开 YAML View，找到 ``spec.pools[n].securityContext``
   和 ``spec.console.securityContext`` 对象。
   根据 OpenShift 集群的 SCC，修改 ``securityContext`` 设置以使用受支持的 UID。

点击 :guilabel:`Create`，按指定配置创建 MinIO 租户。
使用 MinIO Root User secret 中指定的凭证访问 MinIO Server。

3) 连接到租户
~~~~~~~~~~~~~

MinIO Operator 会为 MinIO 租户创建服务。
使用 ``oc get svc -n NAMESPACE`` 命令查看已部署的服务：

.. code-block:: shell
   :class: copyable

   oc get svc -n minio-tenant-1

.. code-block:: shell

   NAME                               TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)          AGE
   minio                              LoadBalancer   10.97.114.60     <pending>     443:30979/TCP    2d3h
   minio-tenant-1-console             LoadBalancer   10.106.103.247   <pending>     9443:32095/TCP   2d3h
   minio-tenant-1-hl                  ClusterIP      None             <none>        9000/TCP         2d3h
   minio-tenant-1-log-hl-svc          ClusterIP      None             <none>        5432/TCP         2d3h
   minio-tenant-1-log-search-api      ClusterIP      10.103.5.235     <none>        8080/TCP         2d3h
   minio-tenant-1-prometheus-hl-svc   ClusterIP      None             <none>        9090/TCP         7h39m

- ``minio`` 服务对应 MinIO 租户服务。
  应用应使用该服务对 MinIO 租户执行操作。
 
- ``*-console`` 服务对应 :minio-git:`MinIO Console <console>`。
  管理员应使用该服务访问 MinIO Console，
  并对 MinIO 租户执行管理操作。

其余服务用于支撑租户运行，不面向用户或管理员直接使用。
 
默认情况下，每个服务仅在 Kubernetes 集群内部可见。
部署在集群内部的应用可以通过 ``CLUSTER-IP`` 访问这些服务。

集群外部的应用可以通过 ``EXTERNAL-IP`` 访问这些服务。
该值仅会在配置了 Ingress 或类似网络访问服务的 Kubernetes 集群中填充。
Kubernetes 提供了多种服务外部访问配置方式。
有关配置服务外部访问的更完整信息，请参阅 Kubernetes 文档中的
:kube-docs:`Publishing Services (ServiceTypes) <concepts/services-networking/service/#publishing-services-service-types>` 
和 :kube-docs:`Ingress <concepts/services-networking/ingress/>`。
