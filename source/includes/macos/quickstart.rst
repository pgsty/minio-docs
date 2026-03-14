.. _quickstart-macos:

.. |OS| replace:: MacOS

本流程会在 |OS| 上部署一个 :ref:`单机单盘 <minio-installation-comparison>` MinIO server，
用于 MinIO 对象存储及其 S3 兼容 API 层的早期开发和评估。

如需面向生产环境的部署说明，请参阅 :ref:`deploy-minio-distributed`。

前提条件
--------

- 对用户家目录具有读、写和执行权限
- 熟悉 Terminal 的使用

流程
----

#. **安装 MinIO Server**

   .. include:: /includes/macos/common-installation.rst
      :start-after: start-install-minio-binary-desc
      :end-before: end-install-minio-binary-desc

 
#. **启动 MinIO Server**

   .. include:: /includes/macos/common-installation.rst
      :start-after: start-run-minio-binary-desc
      :end-before: end-run-minio-binary-desc

#. **将浏览器连接到 MinIO 服务端**

   打开浏览器（例如 Safari），访问 ``https://127.0.0.1:9000``，
   或 :mc:`minio server` 命令输出中列出的任一 Console 地址，即可进入 :ref:`minio-console`。
   例如，示例输出中的 :guilabel:`Console: http://192.0.2.10:9001 http://127.0.0.1:9001`
   就表示有两个可用于连接 Console 的地址。

   虽然端口 ``9000`` 用于连接 API，
   但 MinIO 会自动将浏览器访问重定向到 MinIO Console。

#. `(Optional)` 安装 MinIO Client

   :ref:`MinIO Client <minio-client>` 允许你在命令行中操作 MinIO 存储卷。

   .. tab-set::

      .. tab-item:: Homebrew

         运行以下命令，使用 `Homebrew <https://brew.sh>`_ 安装最新稳定版 MinIO Client 软件包。

         .. code-block:: shell
            :class: copyable

            brew install minio/stable/mc

         使用该命令时，运行：
         
         .. code-block::
            
            mc {command} {flag}

      .. tab-item:: Binary (arm64)

         下载适用于 MacOS 的独立 MinIO server，并赋予其可执行权限。
           
         .. code-block:: shell
            :class: copyable

            curl -O https://dl.min.io/client/mc/release/darwin-arm64/mc
            chmod +x mc
            sudo mv mc /usr/local/bin/mc
   
         使用该命令时，运行：
         
         .. code-block:: shell
            
            mc {command} {flag}

      .. tab-item:: Binary (amd64)

         下载适用于 MacOS 的独立 MinIO server，并赋予其可执行权限。

         .. code-block:: shell
            :class: copyable

            curl -O https://dl.min.io/client/mc/release/darwin-amd64/mc
            chmod +x mc
            sudo mv mc /usr/local/bin/mc

         使用该命令时，运行：
         
         .. code-block:: shell
            
            mc {command} {flag}
            
   使用 :mc:`mc alias set` 快速完成认证并连接到 MinIO 部署。

   .. code-block:: shell
      :class: copyable

      mc alias set local http://127.0.0.1:9000 minioadmin minioadmin
      mc admin info local

   :mc:`mc alias set` 需要四个参数：

   - 别名名称
   - MinIO server 的主机名或 IP 地址及端口
   - MinIO :ref:`user <minio-users>` 的 Access Key
   - MinIO :ref:`user <minio-users>` 的 Secret Key

   关于该命令的更多细节，请参阅 :ref:`alias`。

.. rst-class:: section-next-steps
   
后续步骤
--------

- :ref:`将应用连接到 MinIO <minio-drivers>`
- :ref:`配置对象保留 <minio-object-retention>`
- :ref:`配置安全能力 <minio-authentication-and-identity-management>`
- :ref:`为生产环境部署 MinIO <deploy-minio-distributed>`
