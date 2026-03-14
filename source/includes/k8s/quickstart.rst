.. _quickstart-kubernetes:

.. |OS| replace:: Kubernetes

本流程会在 |OS| 上部署一个 单机单盘 MinIO Server，
用于 MinIO 对象存储及其 S3 兼容 API 层的早期开发与评估。

如需在 Kubernetes 上部署和管理可用于生产的 MinIO 租户，
请使用 :ref:`MinIO Operator <minio-operator-installation>`。

前提条件
--------

- 现有 Kubernetes 部署，其中*至少*有一个 Worker Node 配置了本地直连驱动器。
- 已在本地安装 ``kubectl``，并完成配置，可在目标 Kubernetes 部署上创建和访问资源。
- 熟悉 Kubernetes 环境。
- 熟悉 Terminal 或 Shell 环境。

流程
----

#. **下载 MinIO 对象**

   .. tab-set::

      .. tab-item:: 下载 MinIO Kubernetes 对象定义

         将 `minio-dev.yaml <https://raw.githubusercontent.com/minio/docs/master/source/extra/examples/minio-dev.yaml>`__
         下载到你的主机：

         .. code-block:: shell
            :class: copyable

            curl https://raw.githubusercontent.com/minio/docs/master/source/extra/examples/minio-dev.yaml -O

         该文件定义了两个 Kubernetes 资源：

         - 一个新的命名空间 ``minio-dev``；
         - 一个使用 Worker Node 上驱动器或卷提供数据服务的 MinIO pod。

         如需更详细的对象说明，请选择 :guilabel:`Overview of the MinIO Object YAML`。

      .. tab-item:: MinIO Object YAML 概览

         ``minio-dev.yaml`` 包含以下 Kubernetes 资源：

         .. literalinclude:: /extra/examples/minio-dev.yaml
            :language: yaml

         该对象会部署两个资源：

         - 一个新的命名空间 ``minio-dev``；
         - 一个使用 Worker Node 上驱动器或卷提供数据服务的 MinIO pod。

         MinIO 资源定义使用 Kubernetes
         :kube-docs:`Node Selectors and Labels <concepts/scheduling-eviction/assign-pod-node/#built-in-node-labels>`
         将 pod 限制到具有匹配主机名标签的节点上。
         使用 ``kubectl get nodes --show-labels`` 可查看集群中每个节点上的全部标签。

         MinIO Pod 使用 :kube-docs:`hostPath <concepts/storage/volumes/#hostpath>` 卷来存储数据。
         此路径 *必须* 对应 Kubernetes worker 节点上的本地驱动器或文件夹。

         如果你熟悉 Kubernetes 调度和卷供给机制，
         可以修改 ``spec.nodeSelector``、``volumeMounts.name`` 和 ``volumes`` 字段，
         以满足更具体的需求。

#. **应用 MinIO Object Definition**

   以下命令会应用 ``minio-dev.yaml`` 配置，并将对象部署到 Kubernetes：

   .. code-block:: shell
      :class: copyable

      kubectl apply -f minio-dev.yaml

   命令输出应类似如下：

   .. code-block:: shell

      namespace/minio-dev created
      pod/minio created

   你可以运行 ``kubectl get pods`` 来验证 pod 状态：

   .. code-block:: shell
      :class: copyable

      kubectl get pods -n minio-dev

   输出应类似如下：

   .. code-block:: shell

      NAME    READY   STATUS    RESTARTS   AGE
      minio   1/1     Running   0          77s

   你也可以使用以下命令获取 pod 状态的详细信息：

   .. code-block:: shell
      :class: copyable

      kubectl describe pod/minio -n minio-dev

      kubectl logs pod/minio -n minio-dev

#. **临时访问 MinIO S3 API 和 Console**

   使用 ``kubectl port-forward`` 命令，
   将 MinIO pod 的流量临时转发到本地机器：

   .. code-block:: shell
      :class: copyable

      kubectl port-forward pod/minio 9000 9090 -n minio-dev
   
   该命令会在 shell 保持运行期间，
   将 pod 的 ``9000`` 和 ``9090`` 端口转发到本地机器上的同名端口。
   ``kubectl port-forward`` 仅在当前 shell 会话处于活动状态时生效。
   终止该会话后，本地端口也会关闭。

   .. note::
      
      本流程后续步骤都假定 ``kubectl port-forward`` 命令保持运行。

      如果要为 pod 配置长期访问方式，
      请在 Kubernetes 中配置 :kube-docs:`Ingress <concepts/services-networking/ingress/>`
      或类似的网络控制组件，以便将流量路由到该 pod。
      Ingress 配置超出本文档范围。

#. **将浏览器连接到 MinIO 服务端**

   在本地机器上打开浏览器并访问 ``http://127.0.0.1:9001``，
   即可进入 :ref:`minio-console`。

   使用 ``minioadmin | minioadmin`` 登录 Console。
   这是默认的 :ref:`root user <minio-users-root>` 凭证。

#. **(Optional) 连接 MinIO Client**

   如果本地机器已经 :ref:`安装 <mc-install>` 了 :mc:`mc`，
   请使用 :mc:`mc alias set` 命令完成认证并连接到 MinIO 部署：

   .. code-block:: shell
      :class: copyable

      mc alias set k8s-minio-dev http://127.0.0.1:9000 minioadmin minioadmin
      mc admin info k8s-minio-dev

   - 别名名称
   - MinIO Server 的主机名或 IP 地址及端口
   - MinIO :ref:`user <minio-users>` 的 Access Key
   - MinIO :ref:`user <minio-users>` 的 Secret Key

.. rst-class:: section-next-steps
   
后续步骤
--------

- :ref:`将应用连接到 MinIO <minio-drivers>`
- :ref:`配置对象保留 <minio-object-retention>`
- :ref:`配置安全能力 <minio-authentication-and-identity-management>`
- :ref:`为生产环境部署 MinIO <deploy-minio-distributed>`
