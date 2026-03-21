==============
MinIO 服务端
==============

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: minio

MinIO Server
------------

:mc:`minio server` 命令用于启动 MinIO server 进程：

.. code-block:: shell
   :class: copyable

   minio server /mnt/disk{1...4}

有关在裸金属环境中部署 :mc:`minio server` 的示例，请参阅 :ref:`minio-installation`。

有关在 Kubernetes 环境中部署 :mc:`minio server` 的示例，请参阅 :ref:`Deploying a MinIO Tenant <minio-k8s-deploy-minio-tenant>`。


.. _minio-server-parameters:

语法
~~~~

.. mc:: minio server

启动 ``minio`` server 进程。

该命令语法如下：

.. code-block:: shell
   :class: copyable

   minio server [FLAGS] HOSTNAME/DIRECTORIES [HOSTNAME/DIRECTORIES..]

该命令接受以下参数：

.. mc-cmd:: HOSTNAME

   :mc:`minio server` 进程的主机名。

   对于单机部署，此字段为 *可选*。
   仅使用 :mc-cmd:`~minio server DIRECTORIES` 参数即可启动一个单机 :mc:`~minio server` 进程。

   对于分布式部署，请指定部署中每个 :mc:`minio server` 的主机名。
   这一组 :mc:`minio server` 进程共同表示一个 :ref:`服务器池 <minio-intro-server-pool>`。

   :mc-cmd:`~minio server HOSTNAME` 支持 MinIO 扩展记法 ``{x...y}``，用于表示按顺序排列的一组主机名。
   MinIO *要求* 使用连续主机名，以识别该集合中的每个 :mc:`minio server` 进程。

   例如，``https://minio{1...4}.example.net`` 会扩展为：

   - ``https://minio1.example.net``
   - ``https://minio2.example.net``
   - ``https://minio3.example.net``
   - ``https://minio4.example.net``

   你必须在 服务器池 中每台主机上，使用 *相同* 的 :mc-cmd:`~minio server HOSTNAME` 和 :mc-cmd:`~minio server DIRECTORIES` 组合来运行 :mc:`minio server` 命令。

   每增加一个 ``HOSTNAME/DIRECTORIES`` 对，就表示增加一个 Server Set，以用于 MinIO 部署的水平扩展。
   有关 服务器池s 的更多信息，请参阅 :ref:`服务器池 <minio-intro-server-pool>`。

.. mc-cmd:: DIRECTORIES
   :required:

   :mc:`minio server` 进程用作存储后端的目录或驱动器。

   :mc-cmd:`~minio server DIRECTORIES` 支持 MinIO 扩展记法 ``{x...y}``，用于表示按顺序排列的一组文件夹或驱动器。
   例如，``/mnt/disk{1...4}`` 会扩展为：

   - ``/mnt/disk1``
   - ``/mnt/disk2``
   - ``/mnt/disk3``
   - ``/mnt/disk4``

   首次启动 :mc:`minio <minio server>` 进程时，:mc-cmd:`~minio server DIRECTORIES` 路径 *必须* 为空。

   :mc:`minio server` 进程至少需要 4 个驱动器或目录才能启用 :ref:`erasure coding <minio-erasure-coding>`。

   .. important::

      MinIO 建议使用本地直连驱动器，即 :mc-cmd:`~minio server DIRECTORIES` 路径应指向主机上的各个驱动器。
      MinIO *不建议* 使用网络附加存储，因为与本地直连存储相比，网络延迟会降低这些驱动器的性能。

      在开发或评估场景中，你可以在单个物理卷上指定多个逻辑目录或分区，以便在部署中启用纠删码。

      对于生产环境，MinIO **不建议** 在单个物理磁盘上使用多个逻辑目录或分区。
      虽然 MinIO 支持这些配置，但潜在的成本节省伴随着可靠性下降的风险。


.. mc-cmd:: --address
   :optional:

   将 :mc:`minio <minio server>` server 进程绑定到特定网络地址和端口号。
   将地址和端口指定为 ``ADDRESS:PORT``，其中 ``ADDRESS`` 是 IP 地址或主机名，``PORT`` 是主机系统上有效且开放的端口。
   MinIO 同时支持 IPv4 和 IPv6 寻址，前提是指定地址可路由且可解析。

   若要为主机上配置的所有 IP 地址或主机名修改端口号，仅指定 ``:PORT`` 即可，其中 ``PORT`` 是主机上有效且开放的端口。

   .. versionchanged:: RELEASE.2023-01-02T09-40-09Z

      你可以通过配置 hosts 文件，让 MinIO 仅监听特定 IP。
      例如，如果机器的 `/etc/hosts` 文件包含以下内容：

      .. code-block:: shell

         127.0.1.1       minioip
         127.0.1.2       minioip

      类似下面的命令会在两个已配置 IP 地址的 ``9000`` 端口上监听 API 调用。

      .. code-block:: shell

         minio server --address "minioip:9000" ~/miniodirectory

   若省略该参数，:mc:`minio <minio server>` 会绑定到主机上所有已配置 IPv4 地址、IPv6 地址和主机名的 ``9000`` 端口。

.. mc-cmd:: --console-address
   :optional:

   为内置 MinIO Console 指定静态端口。

   省略时，MinIO 会在 server 启动时生成动态端口。
   MinIO server 会将该端口输出到系统日志。

.. mc-cmd:: --ftp
   :optional:

   启用并配置 File Transfer Protocol（``FTP``）或 File Transfer Protocol over SSL/TLS（``FTPS``）服务器。
   多次使用该 flag 可按键值对指定地址端口、被动端口范围或 TLS 证书与密钥。

   有效键如下：

   - ``address``，用于指定服务器使用的单个端口，通常为 ``8021``

   - *(Optional)* ``passive-port-range``，用于限制服务器可用于传输数据的端口范围，例如防火墙规则较严格、限制 FTP 服务器可为连接请求端口时

   - *(Optional)* ``tls-private-key``，用于指定用户私钥路径，以便通过 TLS 访问 MinIO 部署

     与 ``tls-public-cert`` 配合使用。

   - *(Optional)* ``tls-public-cert``，用于指定证书路径，以便通过 TLS 访问 MinIO 部署

     与 ``tls-private-key`` 配合使用。

   对于已启用 TLS 的 MinIO 部署，省略 ``tls-private-key`` 和 ``tls-public-key``，可让 MinIO 使用部署默认 TLS 密钥。
   更多信息请参阅 :ref:`minio-tls`。
   仅当你要使用与 MinIO 默认值不同的一组 TLS 证书和密钥时，才需要指定证书与私钥（例如使用不同域名）。

   例如：

   .. code-block:: shell
      :class: copyable

      minio server http://server{1...4}/disk{1...4} \
      --ftp="address=:8021"                         \
      --ftp="passive-port-range=30000-40000"        \
      --ftp="tls-private-key=path/to/private.key"   \
      --ftp="tls-public-cert=path/to/public.crt"    \
      ...

.. mc-cmd:: --sftp
   :optional:

   启用并配置 SSH File Transfer Protocol（``SFTP``）服务器。
   可多次使用以指定所需的每个键值对。

   下表列出有效键。

   .. list-table::
      :header-rows: 1
      :widths: 30 30 40
      :width: 100%

      * - 键
        - 说明
        - 有效值

      * - ``address``
        - 用于连接到 SFTP 的端口。
        - 任意有效端口号，通常为 ``8022``。

      * - ``ssh-private-key``
        - 用户私钥文件路径。
        - 用于密钥文件的绝对路径，或相对于当前位置的相对路径。

      * - ``trusted-user-ca-key``
        - 指定一个文件，其中包含受信任证书颁发机构的公钥，该机构可对用于身份验证的用户证书签名。
          文件必须包含 `user principals list <https://man.openbsd.org/ssh-keygen#CERTIFICATES>`__，且列表必须包含可使用该密钥进行身份验证的用户。
        - 用户受信任证书颁发机构公钥文件的绝对路径，或相对于当前位置的相对路径。

      * - ``pub-key-algos``
        - 以逗号分隔的公钥算法列表。
        -
          .. code-block:: text

             ssh-ed25519
             sk-ssh-ed25519@openssh.com
             sk-ecdsa-sha2-nistp256@openssh.com
             ecdsa-sha2-nistp256
             ecdsa-sha2-nistp384
             ecdsa-sha2-nistp521
             rsa-sha2-256
             rsa-sha2-512
             ssh-rsa
             ssh-dss

      * - ``kex-algos``
        - 按优先级顺序排列的、以逗号分隔的密钥交换算法列表。
        -
          .. code-block:: text

             curve25519-sha256
             curve25519-sha256@libssh.org
             ecdh-sha2-nistp256
             ecdh-sha2-nistp384
             ecdh-sha2-nistp521
             diffie-hellman-group14-sha256
             diffie-hellman-group16-sha512
             diffie-hellman-group14-sha1
             diffie-hellman-group1-sha1

      * - ``cipher-algos``
        - 以逗号分隔的支持的加密算法列表
        -
          .. code-block:: text

            aes128-ctr
            aes192-ctr
            aes256-ctr
            aes128-gcm@openssh.com
            aes256-gcm@openssh.com
            chacha20-poly1305@openssh.com
            arcfour256
            arcfour128
            arcfour
            aes128-cbc
            3des-cbc

      * - ``mac-algos``
        - 按优先级顺序排列的、以逗号分隔的 MAC 算法列表。
          基于 `RFC 4253 section 6.4 <https://www.rfc-editor.org/rfc/rfc4253>`__，但 ``hmac-md5`` 变体除外（已终止生命周期）。
        -
          .. code-block:: text

             hmac-sha2-256-etm@openssh.com
             hmac-sha2-512-etm@openssh.com
             hmac-sha2-256
             hmac-sha2-512
             hmac-sha1
             hmac-sha1-96

      * - ``disable-password-auth``
        - 禁用密码认证。
        - ``true``

   例如：

   .. code-block:: shell
      :class: copyable

      minio server http://server{1...4}/disk{1...4}                                 \
      --sftp="address=:8022" --sftp="ssh-private-key=/home/miniouser/.ssh/id_rsa"   \
      --sftp="kex-algos=diffie-hellman-group14-sha256,curve25519-sha256@libssh.org" \
      ...

.. mc-cmd:: --certs-dir, -S
   :optional:

   指定包含证书的文件夹路径，:mc:`minio` 进程使用该路径中的证书配置 TLS/SSL 连接。

   指定文件夹的内容必须符合 :ref:`default path structure <minio-tls-user-generated>`。
   例如，``--certs-dir /etc/minio`` 的路径内容应类似如下：

   .. code-block:: shell

      /etc/minio
        private.key
        public.crt
        domain.tld/
          private.key
          public.crt
        CAs/
          full-chain-ca.crt

   省略时使用默认目录路径：

   - Linux/macOS: ``${HOME}/.minio/certs``
   - Windows: ``%%USERPROFILE%%\.minio\certs``.

   有关 TLS/SSL 连接的更多信息，请参阅 :ref:`minio-TLS`。

   .. important::

      :minio-release:`MinIO Server RELEASE.2023-12-09T18-17-51Z <RELEASE.2023-12-09T18-17-51Z>` 移除了已弃用参数 ``--config-dir | -C``。
      使用该 flag 的部署可能会在未启用 TLS 的情况下启动。
      请将这些参数替换为 ``--certs-dir | -S`` 并重启，以重新启用 TLS。

.. mc-cmd:: --quiet
   :optional:

   禁用启动信息输出。

.. mc-cmd:: --anonymous
   :optional:

   在日志中隐藏敏感信息。

.. mc-cmd:: --json
   :optional:

   以 ``JSON`` 格式输出 server 日志和启动信息。

.. note::

   你可以通过在 :envvar:`MINIO_OPTS` 环境变量中设置上述任意 ``minio`` 参数来定义它们。
   该变量的值是一个字符串，包含你希望在启动 MinIO Server 时设置的上述参数及其对应取值。

设置
----

你可以通过定义额外的 :ref:`Configuration Values <minio-server-configuration-options>` 或 :ref:`Environment Variables <minio-server-environment-variables>`，对 MinIO 服务端进程执行其他自定义配置。

许多配置值和环境变量定义的是同一个值。
如果同时设置配置值及其对应环境变量，MinIO 使用环境变量中的值。


.. toctree::
   :titlesonly:
   :hidden:

   /reference/minio-server/settings
   /reference/minio-server/settings/core
   /reference/minio-server/settings/root-credentials
   /reference/minio-server/settings/storage-class
   /reference/minio-server/settings/console
   /reference/minio-server/settings/metrics-and-logging
   /reference/minio-server/settings/notifications
   /reference/minio-server/settings/iam
   /reference/minio-server/settings/ilm
   /reference/minio-server/settings/kes
   /reference/minio-server/settings/object-lambda
   /reference/minio-server/settings/deprecated
