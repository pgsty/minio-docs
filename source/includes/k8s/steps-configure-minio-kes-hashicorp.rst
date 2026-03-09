#. 查看 Tenant CRD

   查看 :ref:`Tenant CRD <minio-operator-crd>` 中的 ``TenantSpec.kes`` 对象、
   ``TenantSpec.configuration`` 对象，以及 :minio-docs:`KES Configuration 参考</kes/tutorials/configuration>`。

   在继续之前，你必须先准备好所选外部 Key Management Service 所需的全部配置。

#. 创建或修改 Tenant YAML，按需设置 ``KesConfig`` 的值：

   你必须修改 Tenant YAML 或 ``Kustomize`` 模板，以反映所需的 KES 配置。
   以下示例摘自 :minio-git:`MinIO Operator Kustomize 示例 </operator/blob/master/examples/kustomization/tenant-kes-encryption/tenant.yaml>`

   .. code-block:: yaml

      kes:
         image: "" # minio/kes:2024-06-17T15-47-05Z
         env: [ ]
         replicas: 2
         kesSecret:
            name: kes-configuration
         imagePullPolicy: "IfNotPresent"

   ``kes-configuration`` secret 必须引用一个 Kubernetes Opaque Secret，
   其中的 ``stringData`` 对象需要以 ``server-config.yaml`` 的形式包含完整 KES 配置。
   ``keystore`` 字段必须包含你所选 Key Management System 的完整配置。

   更多说明请参阅 :minio-git:`该 Kustomize 示例 <operator/blob/master/examples/kustomization/tenant-kes-encryption/kes-configuration-secret.yaml>`。

#. 创建或修改 Tenant YAML，按需设置 ``TenantSpec.configuration`` 的值。

   TODO

#. 生成新的加密密钥

   .. include:: /includes/k8s/common-minio-kes.rst
      :start-after: start-kes-generate-key-desc
      :end-before: end-kes-generate-key-desc

#. 为存储桶启用 SSE-KMS

   .. include:: /includes/k8s/common-minio-kes.rst
      :start-after: start-kes-enable-sse-kms-desc
      :end-before: end-kes-enable-sse-kms-desc
