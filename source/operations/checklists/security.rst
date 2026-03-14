.. _minio-security-checklist:

============
安全检查清单
============

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

在为生产级分布式 MinIO 部署规划安全配置时，请使用以下检查清单。

必做步骤
--------

.. list-table::
   :widths: auto
   :width: 100%

   * - :octicon:`circle`
     - 在 MinIO 或所选的第三方身份提供商（LDAP/Active Directory 或 OpenID）中定义组策略

   * - :octicon:`circle`
     - 在 MinIO 或所选的第三方身份提供商上定义单个访问策略

   * - :octicon:`circle`
     - （仅 Kubernetes 部署）将租户配置为使用所选的第三方身份提供商

   * - :octicon:`circle`
     - 在防火墙中放行到 MinIO 服务端 S3 API 监听端口的 TCP 流量（默认：``9000``）。

   * - :octicon:`circle`
     - 在防火墙中放行到 :ref:`MinIO Server Console 监听端口 <minio-console-port-assignment>` 的 TCP 流量（推荐默认值：``9090``）。


:ref:`静态数据加密 <minio-sse>`
------------------------------------------------------------

MinIO 通过 Key Encryption Service (KES) 支持以下外部 KMS 提供方：

- :ref:`HashiCorp Vault Root KMS <minio-sse-vault>`
- :ref:`AWS Root KMS <minio-sse-aws>`
- :ref:`Google Cloud Platform Secret Manager Root KMS <minio-sse-gcp>`
- :ref:`Azure Key Vault Root KMS <minio-sse-azure>`

.. list-table::
   :widths: auto
   :width: 100%

   * - :octicon:`circle`
     - 下载并安装 MinIO Key Encryption Service (KES)

   * - :octicon:`circle`
     - 启用 TLS

   * - :octicon:`circle`
     - 为 KES 生成私钥和公钥

   * - :octicon:`circle`
     - 为 MinIO 生成私钥和公钥

   * - :octicon:`circle`
     - 创建 KES 配置文件并启动服务

   * - :octicon:`circle`
     - 为密钥管理服务（KMS）生成外部密钥

   * - :octicon:`circle`
     - 将 MinIO 连接到 KES

   * - :octicon:`circle`
     - 启用服务端加密


:ref:`传输中加密（"In flight"） <minio-tls>`
--------------------------------------------------------------------

.. list-table::
   :widths: auto
   :width: 100%

   * - :octicon:`circle`
     - :ref:`启用 TLS <minio-tls>`

   * - :octicon:`circle`
     - 为每个访问 MinIO 的内部和外部域名分别添加证书和密钥

   * - :octicon:`circle`
     - 使用 TLS 1.3 或 TLS 1.2 支持的 cipher 生成 TLS 私钥和公钥

   * - :octicon:`circle`
     - 配置受信任的 Certificate Authority (CA) 存储

   * - :octicon:`circle`
     - 暴露 Kubernetes Service，例如使用 NGINX

   * - :octicon:`circle`
     - （可选）验证证书，例如使用 https://www.sslchecker.com/certdecoder
