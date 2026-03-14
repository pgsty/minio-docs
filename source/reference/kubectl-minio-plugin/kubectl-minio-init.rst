:orphan:

.. _kubectl-minio-init:

=========================
``kubectl minio init``
=========================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: kubectl minio init

说明
----

.. start-kubectl-minio-init-desc

:mc:`kubectl minio init` 命令用于初始化 MinIO Operator。

.. end-kubectl-minio-init-desc

如果 Kubernetes 集群中已安装 MinIO Operator，此命令会将 Operator 升级到与 MinIO 插件版本一致。
有关升级 MinIO Operator 的更多信息，请参见 :ref:`minio-k8s-upgrade-minio-operator`。

语法
----

.. tab-set::

   .. tab-item:: 示例

      以下命令会初始化一个运行 |operator-version-stable| 的新 MinIO Operator 部署。

      .. code-block:: shell
         :class: copyable

         kubectl minio init

   .. tab-item:: 语法

      该命令的语法如下：

      .. code-block:: shell
         :class: copyable

         kubectl minio init                      \
                       [--cluster-domain]        \
                       [--console-image]         \
                       [--console-tls]           \
                       [--default-kes-image]     \
                       [--default-minio-image]   \
                       [--image]                 \
                       [--image-pull-secret]     \
                       [--namespace]             \
                       [--namespace-to-watch]    \
                       [--output]                \
                       [--prometheus-name]       \
                       [--prometheus-namespace]

参数
----

.. 
   Default values update frequently and can be found in the following files:
   https://github.com/minio/operator/blob/master/kubectl-minio/cmd/init.go
   https://github.com/minio/operator/blob/master/kubectl-minio/cmd/helpers/constants.go

   For minio/console, run ``kubectl minio init -o | grep minio/console``

该命令支持以下参数：

.. mc-cmd:: --cluster-domain
   :optional:

   配置 operator 的 DNS 主机名时使用的域名。
   默认为 ``cluster.local``。

.. mc-cmd:: --console-image
   :optional:

   在 Operator 模式下部署 :minio-git:`Operator Console <operator>` 时使用的镜像，管理员可以通过图形用户界面创建和管理 MinIO 租户。
   默认为与对应 Operator 发布版本中变量 DefaultOperatorImage 绑定的 `版本 <https://github.com/minio/operator/blob/master/kubectl-minio/cmd/helpers/constants.go>`__。

.. mc-cmd:: --console-tls
   :optional:

   .. versionadded:: 4.5.6

   为 Operator Console 启用 TLS。

   默认禁用。

.. mc-cmd:: --default-kes-image
   :optional:

   创建新 MinIO 租户时使用的默认 :minio-git:`kes <kes>` 镜像。
   默认为与对应 Operator 发布版本中变量 DefaultKESImage 绑定的 `版本 <https://github.com/minio/operator/blob/master/kubectl-minio/cmd/helpers/constants.go>`__。

.. mc-cmd:: --default-minio-image
   :optional:

   创建新 MinIO 租户时使用的默认 :minio-git:`minio <minio>` 镜像。
   默认为与对应 Operator 发布版本中变量 DefaultTenantImage 绑定的 `版本 <https://github.com/minio/operator/blob/master/kubectl-minio/cmd/helpers/constants.go>`__。

.. mc-cmd:: --image
   :optional:

   用于部署 operator 的镜像。
   默认为 :minio-git:`Operator 的最新发布版本 <operator/releases/latest>`。

.. mc-cmd:: --image-pull-secret
   :optional:

   用于拉取 :mc-cmd:`~kubectl minio init --image` 的 Secret 密钥。

   由 MinIO 托管的 ``minio/operator`` 镜像*不*受密码保护。
   仅当使用受密码保护的非 MinIO 镜像源时才需要此选项。

.. mc-cmd:: --namespace
   :optional:

   部署 operator 的命名空间。
   默认为 ``minio-operator``。

.. mc-cmd:: --namespace-to-watch
   :optional:

   operator 监听 MinIO 租户的命名空间。
   默认为 ``""``，表示*所有命名空间*。

.. mc-cmd:: --output
   :optional:

   执行 dry run，并将生成的 YAML 输出到 ``STDOUT``。
   使用此选项可自定义 YAML，然后通过 ``kubectl apply -f <FILE>`` 手动应用。

.. mc-cmd:: --prometheus-name
   :optional:

   由 Prometheus Operator 管理的 Prometheus 服务名称。
   默认为 ``PROMETHEUS_NAME``

.. mc-cmd:: --prometheus-namespace
   :optional:

   部署 Prometheus 的命名空间。
   默认为 ``PROMETHEUS_NAMESPACE``

.. mc-cmd:: --sts
   :optional:

   启用 Operator sts (v1alpha1)

   .. versionadded:: 5.0.0
