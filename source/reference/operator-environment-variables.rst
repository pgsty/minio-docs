.. _minio-operator-envvars:

====================================
MinIO Operator 环境变量
====================================

.. default-domain:: minio

.. contents:: 目录

:ref:`MinIO Operator <minio-operator-installation>` 在启动期间使用以下环境变量来设置配置项。
请在 ``minio-operator`` 容器中配置这些变量。

在 Kubernetes 中设置环境变量
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

要设置这些环境变量，请修改 Operator 容器 YAML 中的 ``.spec.env``，或使用以下 ``kubectl`` 命令语法：

.. code-block:: shell
   :class: copyable

   kubectl set env -n minio-operator deployment/minio-operator <ENV_VARIABLE>=<value> ... <ENV_VARIABLE2>=<value2>

替换：

- 如果未使用默认值，将 ``minio-operator`` 替换为你的 Operator 所在命名空间。
- 如果未使用默认值，将 ``deployment/minio-operator`` 替换为你的 Operator 对应 deployment。
  （大多数部署使用默认值。）
- 将 ``<ENV_VARIABLE>`` 替换为要设置或修改的环境变量。
- 将 ``<value>`` 替换为该环境变量要使用的值。

你可以使用空格分隔多个 ``VARIABLE=value`` 键值对，以设置或修改多个环境变量。

可用的 MinIO Operator 环境变量
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. envvar:: MINIO_OPERATOR_CERTIFICATES_VERSION

   指定要使用的证书 API 版本。

   有效值为 ``v1`` 或 ``v1beta1``。

   未指定时，默认使用 Kubernetes 提供的 API。

.. envvar:: MINIO_OPERATOR_RUNTIME

   指定要使用的运行时类型。

   有效值为 ``EKS``、``Rancher`` 或 ``OpenShift``。
   如果以上选项均不适用，则留空。

   当设置为 ``EKS`` 时，:envvar:`MINIO_OPERATOR_CSR_SIGNER_NAME` 必须为 ``beta.eks.amazonaws.com/app-serving``。

.. envvar:: MINIO_OPERATOR_CSR_SIGNER_NAME

   覆盖证书签名请求（CSR）的默认签名者。

   未指定时，默认值为 ``kubernetes.io/kubelet-serving``。

.. envvar:: OPERATOR_CERT_PASSWD
   
   *可选*

   Operator 用于解密其 TLS 证书中私钥的密码。

.. envvar:: OPERATOR_STS_ENABLED

   将 STS Service 切换为 ``on`` 或 ``off``。

   .. versionchanged:: v5.0.11

      未指定时，默认值为 ``on``。

   在 Operator 5.0.11 之前的版本中，默认值为 ``off``。

.. envvar:: MINIO_CONSOLE_DEPLOYMENT_NAME

   用于 Operator Console 的名称。

   未指定时，默认值为 ``operator``。

.. envvar:: MINIO_CONSOLE_TLS_ENABLE

   将 Console TLS 服务切换为 ``on`` 或 ``off``。

   未指定时，默认值为 ``off``。

.. envvar:: MINIO_OPERATOR_IMAGE

   .. versionadded:: v5.0.11

   指定由 Operator 加载的 MinIO 实例 sidecar 容器镜像。

   省略该项则使用 Operator 镜像。

.. envvar:: WATCHED_NAMESPACE

   Operator 监视租户时应监视的命名空间列表，以逗号分隔。
   
   未指定时，默认值为 ``""``，即监视所有命名空间。
