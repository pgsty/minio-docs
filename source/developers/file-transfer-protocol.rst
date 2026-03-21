.. _minio-ftp:

===========================
文件传输协议 (FTP/SFTP)
===========================

.. default-domain:: minio

.. container:: extlinks-video

   - `使用 FTP 和 SFTP 与 MinIO 进行文件传输 <https://www.youtube.com/watch?v=lNZyL8wD-lI>`__

.. contents:: 目录
   :local:
   :depth: 2

.. tab-set::

   .. tab-item:: Kubernetes
      :sync: k8s

      从 MinIO Operator 5.0.7 和 :minio-release:`MinIO Server RELEASE.2023-04-20T17-56-55Z <RELEASE.2023-04-20T17-56-55Z>` 起，你可以使用 SSH 文件传输协议 (SFTP) 与 MinIO Operator 租户部署中的对象交互。

      互联网工程任务组 (IETF) 将 SFTP 定义为 SSH 2.0 的扩展。
      它允许通过 SSH 进行文件传输，可用于 :ref:`传输层安全 (TLS) <minio-tls>` 和虚拟专用网络 (VPN) 场景。

      启用 SFTP 不会影响其他 MinIO 功能。

   .. tab-item:: 裸金属
      :sync: baremetal

      从 :minio-release:`MinIO Server RELEASE.2023-04-20T17-56-55Z <RELEASE.2023-04-20T17-56-55Z>` 起，你可以使用文件传输协议 (FTP) 与 MinIO 部署中的对象交互。

      启动服务器时，你必须显式启用 FTP 或 SFTP。
      启用任一服务器类型都不会影响其他 MinIO 功能。

      本页下文统一使用缩写 FTP，但你可以使用下文描述的任意受支持 FTP 协议。

支持的协议
----------

.. tab-set::

   .. tab-item:: Kubernetes
      :sync: k8s

      MinIO Operator 仅支持配置 SSH 文件传输协议 (SFTP)。

   .. tab-item:: 裸金属
      :sync: baremetal

      启用后，MinIO 支持通过以下协议进行 FTP 访问：

      - SSH 文件传输协议 (SFTP)

        互联网工程任务组 (IETF) 将 SFTP 定义为 SSH 2.0 的扩展。
        SFTP 允许通过 SSH 进行文件传输，可用于 :ref:`传输层安全 (TLS) <minio-tls>` 和虚拟专用网络 (VPN) 场景。

        你的 FTP 客户端必须支持 SFTP。

      - 通过 SSL/TLS 的文件传输协议 (FTPS)
      
        FTPS 允许在标准 FTP 通信通道上使用 TLS 证书进行加密传输。
        不应将 FTPS 与 SFTP 混淆，因为 FTPS 并不通过 Secure Shell (SSH) 通信。

        你的 FTP 客户端必须支持 FTPS。

      - 文件传输协议 (FTP)
      
        不加密的文件传输。

        MinIO **不**建议使用未加密的 FTP 进行文件传输。

支持的命令
----------

启用后，MinIO 支持以下 SFTP 操作：

- ``get``
- ``put``
- ``ls``
- ``mkdir``
- ``rmdir``
- ``delete``

MinIO 不支持 ``append`` 或 ``rename`` 操作。

注意事项
--------

版本控制
~~~~~~~~

SFTP 客户端只能操作对象的 :ref:`当前版本 <minio-bucket-versioning>`。
具体来说：

- 对于读取操作，MinIO 只会向 SFTP 客户端返回所请求对象的最新版本。
- 对于写入操作，MinIO 会应用正常的版本控制行为，并在指定命名空间中创建新的对象版本。
  ``rm`` 和 ``rmdir`` 操作会创建 ``DeleteMarker`` 对象。

身份验证与访问控制
~~~~~~~~~~~~~~~~~~

SFTP 访问与其他 S3 客户端一样，使用相同的身份验证机制。
MinIO 支持以下身份验证提供方：

- :ref:`MinIO IDP <minio-internal-idp>` 用户及其服务账户
- :ref:`Active Directory/LDAP <minio-external-identity-management-ad-ldap>` 用户及其服务账户
- :ref:`OpenID/OIDC <minio-external-identity-management-openid>` 服务账户

:ref:`STS <minio-security-token-service>` 凭证**不能**通过 SFTP 访问存储桶或对象。

已通过身份验证的用户可依据分配给该用户或其父用户账号的 :ref:`策略 <minio-policy>` 访问存储桶和对象。

SFTP 协议不需要任何 ``admin:*`` :ref:`权限 <minio-policy-mc-admin-actions>`。
你不能通过 SFTP 执行其他 MinIO 管理操作。

前提条件
--------

.. tab-set::

   .. tab-item:: Kubernetes
      :sync: k8s

      - MinIO Operator v5.0.7 或更高版本。
      - 为服务器启用一个 SFTP 端口 (8022)。
      - 一个用于 SFTP 命令的端口，以及一个端口范围，用于允许 SFTP 服务器在数据传输期间按需请求使用。

   .. tab-item:: 裸金属
      :sync: baremetal

      - MinIO RELEASE.2023-04-20T17-56-55Z 或更高版本。
      - 为服务器启用一个 FTP 或 SFTP 端口。
      - 一个用于 FTP 命令的端口，以及一个端口范围，用于允许 FTP 服务器在数据传输期间按需请求使用。

操作步骤
--------

.. tab-set::

   .. tab-item:: Kubernetes
      :sync: k8s

      .. include:: /includes/k8s/file-transfer-protocol-k8s.rst

   .. tab-item:: 裸金属
      :sync: baremetal

      .. include:: /includes/linux/file-transfer-protocol-not-k8s.rst

.. _minio-certificate-key-file-sftp-k8s:

使用证书密钥文件通过 SFTP 连接到 MinIO
--------------------------------------

.. versionadded:: RELEASE.2024-05-07T06-41-25Z


MinIO 支持在 SFTP 上使用基于证书的双向 TLS (mTLS) 身份验证，服务器与客户端会相互验证对方的真实性。

这种身份验证方式需要以下材料：

1. 受信任证书颁发机构的公钥文件
2. 由受信任证书颁发机构签发并签名的 MinIO Server 公钥文件
3. 供通过 SFTP 连接的客户端使用的用户公钥文件。该文件由受信任证书颁发机构签发并签名，并位于用户的 ``.ssh`` 文件夹中（或操作系统中的等效位置）

这些密钥必须包含可使用该密钥进行身份验证的用户的 `principals 列表 <https://man.openbsd.org/ssh-keygen#CERTIFICATES>`__：

.. code-block:: shell
   :class: copyable

   ssh-keygen -s ~/.ssh/ca_user_key -I miniouser -n miniouser -V +1h -z 1 miniouser1.pub

- ``-s`` 指定用于生成此密钥的证书颁发机构公钥路径。
   指定的公钥必须具有包含该用户的 ``principals`` 列表。
- ``-I`` 指定该公钥的密钥标识。
- ``-n`` 创建此密钥有效的 ``user principals`` 列表。
  你必须包含该密钥有效的用户，且该用户必须与 MinIO 中的用户名匹配。
- ``-V`` 限制生成密钥的有效时长。
  在此示例中，该密钥的有效期为一小时。
  请根据需求调整时长。
- ``-z`` 为密钥添加序列号，以便将此生成的公钥与由同一证书颁发机构公钥签名的其他密钥区分开。

MinIO 要求指定用于签发 SFTP 访问证书的证书颁发机构 (Certificate Authority)。
启动或重启 MinIO Server，并通过 ``--sftp="trusted-user-ca-key=PATH"`` 参数指定受信任证书颁发机构公钥的路径：

.. code-block:: shell
   :class: copyable 

   minio server {path-to-server} --sftp="trusted-user-ca-key=/path/to/.ssh/ca_user_key.pub" {...other flags}

通过 SFTP 连接到 MinIO 服务端 时，客户端会先验证 MinIO Server 的证书。
随后，客户端会将自己的证书发送给 MinIO Server。
MinIO Server 会将上面创建的密钥与服务器启动时提供的证书颁发机构公钥进行比对，以验证该密钥。

一旦 MinIO Server 验证了客户端证书，用户就可以通过 SFTP 连接到 MinIO 服务端：

.. code-block:: bash
   :class: copyable
   
   sftp -P <SFTP port> <server IP>

要求使用服务账户或 LDAP 进行身份验证
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

若要强制使用 LDAP 或服务账户凭证进行 SFTP 身份验证，请在用户名后附加后缀。
有效后缀为 ``=ldap`` 或 ``=svc``。

.. code-block:: console

   > sftp -P 8022 my-ldap-user=ldap@[minio@localhost]:/bucket


.. code-block:: console

   > sftp -P 8022 my-ldap-user=svc@[minio@localhost]:/bucket


- 将 ``my-ldap-user`` 替换为要使用的用户名。
- 将 ``[minio@localhost]`` 替换为 MinIO 服务器地址。
