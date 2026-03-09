.. _minio-tls:
.. _minio-TLS-third-party-ca:
.. _minio-tls-user-generated:

========================
网络加密（TLS）
========================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. admonition:: SSL 已弃用
   :class: note

   TLS 是 Secure Socket Layer（SSL）加密的后继方案。
   自 2018 年 6 月 30 日起，SSL 已被完全 `弃用 <https://tools.ietf.org/html/rfc7568>`__。

概览
----

MinIO 支持使用 Transport Layer Security (TLS) 1.2+ 对入站和出站流量进行加密。
MinIO 可以自动检测默认或自定义搜索路径中的证书，并为所有连接启用 TLS。
MinIO 支持客户端发起的 Server Name Indication (SNI) 请求，此时 MinIO 会尝试为客户端指定的主机名定位合适的 TLS 证书。

.. todo: add an image

MinIO *至少* 需要一张默认 TLS 证书，并且可以通过多张 TLS 证书支持 SNI 连接。
MinIO 使用 TLS Subject Alternative Name (SAN) 列表来判断应向客户端返回哪张证书。
如果 MinIO 找不到 SAN 覆盖客户端请求主机名的 TLS 证书，则会使用默认证书并尝试完成握手。

你可以指定一张 TLS 证书，覆盖 MinIO 部署可接受连接的所有 SAN。

这种配置所需设置最少，但会向连接客户端暴露 TLS SAN 中配置的全部主机名。
根据你的 TLS 配置，这些主机名可能包含内部或私有 SAN 域名。

你也可以按域名拆分配置多张 TLS 证书，并为所有未匹配主机名请求保留一张默认证书。
这种配置需要更多设置，但只会暴露返回证书的 TLS SAN 数组中所配置的主机名。

.. _minio-tls-kubernetes:

Kubernetes 上的 MinIO TLS
-------------------------

MinIO Kubernetes Operator 为 MinIO 租户提供三种 TLS 配置方式：

使用 Cluster Signing API 自动启用 TLS
   对于具备有效 :ref:`TLS Cluster Signing Certificate <minio-k8s-deploy-operator-tls>` 的 Kubernetes 集群，MinIO Kubernetes Operator 可以在 :ref:`部署 <minio-k8s-deploy-minio-tenant-security>` 或 :ref:`修改 <minio-k8s-modify-minio-tenant-security>` MinIO 租户时自动生成 TLS 证书。

   Kubernetes TLS API 在生成新的 TLS 证书时，会使用 Kubernetes 集群 Certificate Authority (CA) 的签名算法。
   MinIO 支持的 TLS Cipher Suite 和推荐签名算法完整列表，请参见 :ref:`minio-TLS-supported-cipher-suites`。

   默认情况下，Kubernetes 会在每个 pod 的 ``/var/run/secrets/kubernetes.io/serviceaccount/ca.crt`` 放置一份证书 bundle。
   该 CA bundle 应包含用于签发 MinIO 租户 TLS 证书的集群 CA 或根 CA。
   部署在同一 Kubernetes 集群中的其他应用，可以信任这份集群证书，并通过 :kube-docs:`MinIO service DNS 名称 <concepts/services-networking/dns-pod-service/>` 连接到 MinIO 租户（例如 ``https://minio.minio-tenant-1.svc.cluster-domain.example:443``）。

   .. admonition:: Subject Alternative Name 证书
      :class: note

      如果你使用的是自定义 Subject Alternative Name (SAN) 证书，并且它 *不是* 通配符证书，那么 TLS 证书的 SAN **必须** 覆盖其父节点主机名。
      在没有通配符的情况下，SAN 必须精确匹配，才能成功连接到该租户。

cert-manager 证书管理
   MinIO Operator 支持使用 `cert-manager <https://cert-manager.io/>`__ 作为其内置自动证书管理或用户手动证书管理的完整替代方案。
   关于如何使用 cert-manager 部署 MinIO Operator 和租户，请参见 :ref:`cert-manager 页面 <minio-certmanager>`。

手动证书管理
   Tenant CRD 规范中的 ``spec.externalCertsSecret`` 支持引用类型为 ``opaque`` 或 ``kubernetes.io/tls`` 的 :kube-docs:`secret <concepts/configuration/secret/#secret-types>`，其中包含用于 TLS 的 ``private.key`` 和 ``public.crt``。

   你可以指定多张证书，以支持分配了多个主机名的租户。


自签名、内部、私有证书以及带中间证书的公共 CA
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

如果你为 MinIO 租户部署的是由非全球或非公共 Certificate Authority 签发的证书，*或者* 使用了需要中间证书的公共 CA，则必须将这些 CA 提供给 Operator，以确保它能够信任这些证书。

对于使用不受信任证书部署的租户，Operator 可能会记录与 TLS 证书校验相关的警告。

以下过程会将一个包含 Certificate Authority ``public.crt`` 的 secret 挂载到 MinIO Operator。
你可以在同一证书文件中放入多个 CA，只要保持 ``BEGIN`` 和 ``END`` 分隔符原样不变即可。

1. 创建 ``operator-ca-tls`` secret

   以下命令会在 MinIO Operator 命名空间（``minio-operator``）中创建一个 Kubernetes secret。

   .. code-block:: shell
      :class: copyable

      kubectl create secret generic operator-ca-tls \
         --from-file=public.crt -n minio-operator

   ``public.crt`` 文件必须是包含一个或多个 CA 定义的有效 TLS 证书。

2. 重启 Operator

   创建完成后，你必须重启 Operator 以加载新的 CA：

   .. code-block:: shell
      :class: copyable

      kubectl rollout restart deployments.apps/minio-operator -n minio-operator

第三方 Certificate Authority
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

MinIO Kubernetes Operator 可以在 :ref:`部署 <minio-k8s-deploy-minio-tenant-security>` 或 :ref:`修改 <minio-k8s-modify-minio-tenant-security>` MinIO 租户时自动挂载第三方 Certificate Authority。

你可以随时为租户添加、更新或删除 CA。
要让配置中的 CA 变更生效，必须重启 MinIO 租户。

Operator 会将指定的 CA 放置到每个 MinIO Server pod 上，从而确保所有 pod 拥有一致的受信任 CA 集合。

如果 MinIO Server 无法将传入客户端的 TLS 证书签发者与任一可用 CA 匹配，则会将该连接视为无效并拒绝。

.. _minio-tls-baremetal:

Baremetal 上的 MinIO TLS
------------------------

MinIO Server 会为每个节点搜索 TLS 私钥和证书，并使用这些凭据启用 TLS。
MinIO 会在发现并验证证书后自动启用 TLS。
搜索位置取决于你的 MinIO 配置：

.. tab-set::

   .. tab-item:: 默认路径

      默认情况下，MinIO server 会在以下目录中查找每个节点的 TLS 私钥和证书：

      .. code-block:: shell

         ${HOME}/.minio/certs

      其中 ``${HOME}`` 是运行 MinIO Server 进程的用户主目录。
      如果 ``${HOME}/.minio/certs`` 目录不存在，你可能需要手动创建它。

      对于由 ``systemd`` 管理的部署，该路径必须对应运行 MinIO 进程的 ``USER``。
      如果该用户没有主目录，请改用 :guilabel:`自定义路径` 选项。

   .. tab-item:: 自定义路径

      你可以通过 :mc-cmd:`minio server --certs-dir` 或 ``-S`` 参数指定 MinIO server 搜索证书的路径。

      例如，以下命令片段指示 MinIO 进程使用 ``/opt/minio/certs`` 目录存放 TLS 证书。

      .. code-block:: shell

         minio server --certs-dir /opt/minio/certs ...

      运行 MinIO service 的用户 *必须* 对该目录拥有读写权限。

请将默认域名（例如 ``minio.example.net``）对应的 TLS 证书放入 ``/certs`` 目录，其中私钥命名为 ``private.key``，公钥证书命名为 ``public.crt``。

对于分布式 MinIO 部署，部署中的每个节点都必须具有一致的 TLS 证书配置。

自签名、内部、私有证书以及带中间证书的公共 CA
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

如果你使用的是由非全球或非公共 Certificate Authority 签发的证书，*或者* 使用了需要中间证书的公共 CA，则必须将这些 CA 提供给 MinIO Server。
如果 MinIO server 不具备这些必要 CA，在连接其他服务时可能会返回与 TLS 校验相关的警告或错误。

请将 CA 证书放入 ``/certs/CAs`` 目录。
该目录的根路径取决于你使用的是默认证书路径，还是自定义证书路径（:mc-cmd:`minio server --certs-dir` 或 ``-S``）。

.. tab-set::

   .. tab-item:: 默认证书路径

      .. code-block:: shell

         mv myCA.crt ${HOME}/.minio/certs/CAs

   .. tab-item:: 自定义证书路径

      以下示例假设 MinIO Server 以 ``--certs dir /opt/minio/certs`` 启动：

      .. code-block:: shell

         mv myCA.crt /opt/minio/certs/CAs/

对于自签名证书，Certificate Authority 通常就是用于签署该证书的私钥。

对于由内部、私有或其他非全球 Certificate Authority 签发的证书，请使用签发该证书的同一 CA。
非全球 CA 必须包含从中间证书到根证书的完整信任链。

如果提供的文件不是 X.509 证书，MinIO 会忽略它，并可能在校验由该 CA 签发的证书时返回错误。

第三方 Certificate Authority
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

MinIO Server 会使用主机系统中的受信任根证书存储来校验每个连接客户端提交的 TLS 证书。

请将 CA 证书放入 ``/certs/CAs`` 目录。
该目录的根路径取决于你使用的是默认证书路径，还是自定义证书路径（:mc-cmd:`minio server --certs-dir` 或 ``-S``）。

.. tab-set::

   .. tab-item:: 默认证书路径

      .. code-block:: shell

         mv myCA.crt ${HOME}/certs/CAs

   .. tab-item:: 自定义证书路径

      以下示例假设 MinIO Server 以 ``--certs dir /opt/minio/certs`` 启动：

      .. code-block:: shell

         mv myCA.crt /opt/minio/certs/CAs/

请将每个 CA 的证书文件放入 ``/CAs`` 子目录。
确保 MinIO 部署中的所有主机在该目录下拥有一致的受信任 CA 集合。
如果 MinIO Server 无法将传入客户端的 TLS 证书签发者与任一可用 CA 匹配，则会将该连接视为无效并拒绝。

.. _minio-TLS-supported-cipher-suites:

支持的 TLS Cipher Suite
~~~~~~~~~~~~~~~~~~~~~~~

与 RSA 相比，MinIO 更推荐生成 ECDSA（例如 `NIST P-256 曲线 <https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf>`__）或 EdDSA（例如 :rfc:`Curve25519 <7748>`）TLS 私钥/证书，因为它们的计算开销更低。

MinIO 支持以下由 `Go <https://cs.opensource.google/go/go/+/refs/tags/go1.17.1:src/crypto/tls/cipher_suites.go;l=52>`__ 支持的 TLS 1.2 和 TLS 1.3 cipher suite。列表中使用 :octicon:`star-fill` 标记推荐算法：

.. tab-set::

   .. tab-item:: TLS 1.3

      - ``TLS_CHACHA20_POLY1305_SHA256`` :octicon:`star-fill`
      - ``TLS_AES_128_GCM_SHA256``
      - ``TLS_AES_256_GCM_SHA384``

   .. tab-item:: TLS 1.2

      - ``TLS_ECDHE_ECDSA_WITH_CHACHA20_POLY1305`` :octicon:`star-fill`
      - ``TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256`` :octicon:`star-fill`
      - ``TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384`` :octicon:`star-fill`
      - ``TLS_ECDHE_RSA_WITH_CHACHA20_POLY1305``
      - ``TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256``
      - ``TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384``

.. toctree::
   :hidden:

   /operations/network-encryption/enable-minio-tls
   /operations/network-encryption/enable-multiple-domain-minio-tls
   /operations/network-encryption/cert-manager
