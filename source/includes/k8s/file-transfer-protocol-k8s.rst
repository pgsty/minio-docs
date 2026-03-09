#. 为目标 Tenant 启用 SFTP：

   使用以下 Kubectl 命令编辑 Tenant YAML 配置：

   .. code-block:: yaml

         kubectl edit tenants/my-tenant -n my-tenant-ns

   将 ``my-tenant`` 和 ``my-tenant-ns`` 替换为目标 Tenant 和命名空间。

      在 ``features:`` 部分，将 ``enableSFTP`` 的值设置为 ``true``：

      .. code-block:: yaml

         spec:
            configuration:
               name: my-tenant-env-configuration
            credsSecret:
               name: my-tenant-secret
            exposeServices:
               console: true
               minio: true
            features:
               enableSFTP: true

      Kubectl 会重启 MinIO 以应用更改。

      你也可以在 `Helm chart <https://github.com/minio/operator/blob/8385948929bc95648d1be82d96f829c810519674/helm/tenant/values.yaml>`__ 或 `Kustomize 配置 <https://github.com/minio/operator/blob/8385948929bc95648d1be82d96f829c810519674/examples/kustomization/base/tenant.yaml>`__ 中设置 ``enableSFTP``，为新创建的 Tenant 启用 SFTP。
	 

#. 如有需要，请根据本地策略为 SFTP 端口配置 ingress。

#. 验证配置

   以下 ``kubectl get`` 命令使用 `yq <https://github.com/mikefarah/yq/#install>`__ 显示 ``enableSFTP`` 的值，以确认是否已启用 SFTP：

   .. code-block:: console
      :class: copyable

      kubectl get tenants/my-tenant -n my-tenant-ns -o yaml | yq '.spec.features'

   将 ``my-tenant`` 和 ``my-tenant-ns`` 替换为目标 Tenant 和命名空间。

   如果已启用 SFTP，输出类似如下：

   .. code-block:: console

      enableSFTP: true

#. 使用你偏好的 SFTP 客户端连接到 MinIO 部署。
   你必须以其 :ref:`策略 <minio-policy>` 允许访问目标存储桶和对象的用户身份进行连接。

   连接到 MinIO 部署的具体方式取决于所使用的 SFTP 客户端。
   请参阅该客户端的文档。

   以下示例连接到转发到本地主机系统的 MinIO Tenant SFTP 服务器，并列出名为 ``runner`` 的存储桶内容。

         .. code-block:: console

            > sftp -P 8022 minio@localhost
            minio@localhost's password:
            Connected to localhost.
            sftp> ls runner/
            chunkdocs  testdir

以下 ``kubectl get`` 命令使用 `yq <https://github.com/mikefarah/yq/#install>`__ 显示 ``enableSFTP`` 的值，以确认是否已启用 SFTP：

.. code-block:: console
   :class: copyable

   kubectl get tenants/my-tenant -n my-tenant-ns -o yaml | yq '.spec.features'

将 ``my-tenant`` 和 ``my-tenant-ns`` 替换为目标 Tenant 和命名空间。

如果已启用 SFTP，输出类似如下：

.. code-block:: console

   enableSFTP: true
