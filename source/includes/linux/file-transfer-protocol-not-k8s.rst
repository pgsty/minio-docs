1. 启动 MinIO 并启用 FTP 和/或 SFTP 端口。

   .. tab-set::

      .. tab-item:: FTPS
         :sync: ftps

         以下示例以启用 FTPS 的方式启动 MinIO。

         .. code-block:: shell
            :class: copyable

            minio server http://server{1...4}/disk{1...4} \
            --ftp="address=:8021"                         \
            --ftp="passive-port-range=30000-40000"        \
            --ftp="tls-private-key=path/to/private.key"   \
            --ftp="tls-public-cert=path/to/public.crt"    \
            ...

         .. note:: 

            省略 ``tls-private-key`` 和 ``tls-public-cert`` 可使用 MinIO 默认 TLS 密钥进行 FTPS 连接。
            更多信息请参阅 :ref:`MinIO TLS 文档 <minio-tls>`。

      .. tab-item:: SFTP/FTP
         :sync: sftp

         .. code-block:: shell
            :class: copyable

            minio server http://server{1...4}/disk{1...4}        \
            --ftp="address=:8021"                                \
            --ftp="passive-port-range=30000-40000"               \
            --sftp="address=:8022"                               \
            --sftp="ssh-private-key=/home/miniouser/.ssh/id_rsa" \
            ...
    
         有关使用这些标志启动 MinIO 服务的详细信息，请参阅 :mc-cmd:`minio server --ftp` 和 :mc-cmd:`minio server --sftp`。
         若要通过 TLS (FTPS) 连接到 FTP 端口，除非使用 MinIO 默认 TLS 密钥，否则还需要传入 ``tls-private-key`` 和 ``tls-public-cert`` 键值。

         命令输出应类似如下：

         .. code-block:: shell

            MinIO FTP Server listening on :8021
            MinIO SFTP Server listening on :8022

2. 使用你偏好的 FTP 客户端连接到 MinIO 部署。
   你必须使用其 :ref:`策略 <minio-policy>` 允许访问目标存储桶和对象的用户身份进行连接。

   连接到 MinIO 部署的具体方式取决于所使用的 FTP 客户端。
   请参阅该客户端的文档。

   若要通过 TLS 或 SSH 连接，必须使用支持相应协议的客户端。

3. 连接到 MinIO

   .. tab-set::

      .. tab-item:: SFTP/FTP
         :sync: sftp


         以下示例连接到 SFTP 服务器，并列出名为 ``runner`` 的存储桶内容。

         .. code-block:: console

            > sftp -P 8022 minio@localhost
            minio@localhost's password:
            Connected to localhost.
            sftp> ls runner/
            chunkdocs  testdir


      .. tab-item:: FTPS
         :sync: ftps

         以下示例使用 Linux 的 `FTP CLI client <https://linux.die.net/man/1/ftp>`__，通过 ``minio`` 凭证连接到 MinIO 服务器，并列出名为 ``runner`` 的存储桶内容。

         .. code-block:: shell

            > ftp localhost -P 8021
            Connected to localhost.
            220 Welcome to MinIO FTP Server
            Name (localhost:user): minio
            331 User name ok, password required
            Password:
            230 Password ok, continue
            Remote system type is UNIX.
            Using binary mode to transfer files.
            ftp> ls runner/
            229 Entering Extended Passive Mode (|||39155|)
            150 Opening ASCII mode data connection for file list
            drwxrwxrwx 1 nobody nobody            0 Jan  1 00:00 chunkdocs/
            drwxrwxrwx 1 nobody nobody            0 Jan  1 00:00 testdir/
            ...


4. 下载对象

   .. tab-set:: 

      .. tab-item:: SFTP/FTP
         :sync: sftp

         本示例先列出存储桶中的条目，然后下载该存储桶中的内容。

         .. code-block:: console

            > sftp -P 8022 minio@localhost
            minio@localhost's password:
            Connected to localhost.
            sftp> ls runner/
            chunkdocs  testdir
            sftp> get runner/chunkdocs/metadata metadata
            Fetching /runner/chunkdocs/metadata to metadata
            metadata                               100%  226    16.6KB/s   00:00
            sftp> 

      .. tab-item:: FTPS
         :sync: ftps

         本示例先列出存储桶中的条目，然后下载该存储桶中的内容。

         .. code-block:: console

            > ftp localhost -P 8021
            Connected to localhost.
            220 Welcome to MinIO FTP Server
            Name (localhost:user): minio
            331 User name ok, password required
            Password:
            230 Password ok, continue
            Remote system type is UNIX.
            Using binary mode to transfer files.ftp> ls runner/chunkdocs/metadata
            229 Entering Extended Passive Mode (|||44269|)
            150 Opening ASCII mode data connection for file list
            -rwxrwxrwx 1 nobody nobody           45 Apr  1 06:13 chunkdocs/metadata
            226 Closing data connection, sent 75 bytes
            ftp> get
            (remote-file) runner/chunkdocs/metadata
            (local-file) test
            local: test remote: runner/chunkdocs/metadata
            229 Entering Extended Passive Mode (|||37785|)
            150 Data transfer starting 45 bytes
               45        3.58 KiB/s
            226 Closing data connection, sent 45 bytes
            45 bytes received in 00:00 (3.55 KiB/s)
            ...
