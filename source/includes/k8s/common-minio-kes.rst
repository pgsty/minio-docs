.. start-kes-prereq-hashicorp-vault-desc

本流程假定 Kubernetes 集群能够访问一个现有的
:kes-docs:`受支持 KMS 安装 <#supported-kms-targets>`。

- 对于与 MinIO Tenant 位于同一 Kubernetes 集群内的部署，
  你可以使用 Kubernetes service 名称，
  让 MinIO Tenant 与目标 KMS 服务建立连接。

- 对于位于 Kubernetes 集群外部的部署，
  你必须确保集群支持在 Kubernetes services、pods 与外部网络之间路由通信。
  这可能要求额外配置或部署 Kubernetes 网络组件，
  和/或启用对公网的访问。

关于部署和配置的具体指导，请参考你所选 KMS 方案的文档。

.. end-kes-prereq-hashicorp-vault-desc

.. start-kes-enable-sse-kms-desc

你可以使用 MinIO Tenant Console 或 MinIO :mc:`mc` CLI，
通过生成的密钥启用存储桶默认 SSE-KMS：

.. tab-set::

   .. tab-item:: MinIO Tenant Console

      连接到 :ref:`MinIO Tenant Console service <create-tenant-connect-tenant>` 并登录。
      对于 Kubernetes 集群内部客户端，
      你可以指定 :kube-docs:`service DNS name <concepts/services-networking/dns-pod-service/#a-aaaa-records>`。
      对于 Kubernetes 集群外部客户端，
      请指定通过 Ingress、Load Balancer 或类似 Kubernetes 网络控制组件暴露的服务主机名。

      登录后，新建一个 Bucket，并按你的偏好命名。
      选择齿轮 :octicon:`gear` 图标打开管理视图。

      选择 :guilabel:`Encryption` 字段旁的铅笔 :octicon:`pencil` 图标，
      打开配置存储桶默认 SSE 方案的弹窗。

      选择 :guilabel:`SSE-KMS`，然后输入上一步创建的密钥名称。

      保存更改后，尝试向该存储桶上传一个文件。
      在对象浏览器中查看该文件时，
      请注意侧边栏中的元数据包含了 SSE 加密方案
      以及用于加密该对象的密钥信息。
      这表明该对象已成功加密。

   .. tab-item:: MinIO CLI

      使用 :ref:`MinIO API Service <create-tenant-connect-tenant>`
      为 MinIO 部署创建新的 :ref:`alias <alias>`。
      之后即可使用 :mc:`mc encrypt set`
      为存储桶启用 SSE-KMS 加密：

      .. code-block:: shell
         :class: copyable

         mc alias set k8s https://minio.minio-tenant-1.svc.cluster-domain.example:443 ROOTUSER ROOTPASSWORD

         mc mb k8s/encryptedbucket
         mc encrypt set SSE-KMS encrypted-bucket-key k8s/encryptedbucket

      对于 Kubernetes 集群外部客户端，
      请指定通过 Ingress、Load Balancer 或类似 Kubernetes 网络控制组件暴露的服务主机名。

      使用 :mc:`mc cp` 或任何带有 ``PutObject`` 函数的 S3 兼容 SDK
      将文件写入该存储桶。
      然后你可以对该文件执行 :mc:`mc stat`，
      以确认其关联的加密元数据。

.. end-kes-enable-sse-kms-desc

.. start-kes-generate-key-desc

.. admonition:: 创建密钥前先解封 Vault
   :class: important

   如果你所选的提供方有此要求，
   则必须先解封底层 Vault 实例，
   然后才能创建新的加密密钥。
   更多信息请参考你所选 KMS 方案的文档。

MinIO 要求某个存储桶或对象使用的 |EK| 在执行 |SSE| 操作之前，
必须已经存在于根 KMS 中。
你可以针对 MinIO Tenant 使用 :mc-cmd:`mc admin kms key create` 命令。

在使用 :mc:`mc` 管理 Tenant 之前，
你必须确保本地主机能够访问 MinIO Tenant 的 pods 和 services。
对于 Kubernetes 集群内部主机，
你可以使用 :kube-docs:`service DNS name <concepts/services-networking/dns-pod-service/#a-aaaa-records>`。
对于 Kubernetes 集群外部主机，
请指定通过 Ingress、Load Balancer 或类似 Kubernetes 网络控制组件暴露的服务主机名。

在单独的 Terminal 或 Shell 中运行以下命令：

.. code-block:: shell
   :class: copyable

   # Replace '-n minio' with the namespace of the MinIO deployment
   # If you deployed the Tenant without TLS you may need to change the port range

   # You can validate the ports in use by running
   #  kubectl get svc/minio -n minio

   kubectl port forward svc/minio 443:443 -n minio

在新的 Terminal 或 Shell 窗口中执行以下操作：

- 将本地 :mc:`mc` 客户端连接到 Tenant。
- 创建加密密钥。

关于如何在本地主机上安装 ``mc``，
请参见 :ref:`mc-install`。

.. code-block:: shell
   :class: copyable

   # Replace USERNAME and PASSWORD with a user on the tenant with administrative permissions
   # such as the root user

   mc alias add k8s https://localhost:443 ROOTUSER ROOTPASSWORD

   # Replace my-new-key with the name of the key you want to use for SSE-KMS
   mc admin kms key create k8s encrypted-bucket-key

.. end-kes-generate-key-desc
