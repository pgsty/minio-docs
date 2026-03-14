.. _integrations-nginx-proxy:

======================================
为 MinIO Server 配置 NGINX 代理
======================================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

以下文档提供了在 Linux 环境中配置 NGINX 将请求代理到 MinIO 的基线方案。
这并非一份关于 NGINX、代理或反向代理的一般性完整指南。
请根据你的基础设施需要调整配置。

本文档基于以下前提：

- 已有 `NGINX <http://nginx.org/en/download.html>`__ 部署
- 已有 :ref:`MinIO <minio-installation>` 部署
- 一个可唯一标识该 MinIO 部署的 DNS 主机名

将请求代理到 MinIO 服务端 API 和 MinIO Console 有两种模式：

.. tab-set::

   .. tab-item:: 专用 DNS

      为 MinIO 服务创建或配置一个专用 DNS 名称。

      对于 MinIO Server S3 API，将请求代理到该域名的根路径。
      对于 MinIO Console Web GUI，将请求代理到 ``/minio`` 子路径。

      例如，给定主机名 ``minio.example.net``：
      
      - 将对根路径 ``https://minio.example.net`` 的请求代理到监听于 ``https://minio.local:9000`` 的 MinIO Server。

      - 将对 ``https://minio.example.net/minio/ui`` 子路径的请求代理到监听于 ``https://minio.local:9001`` 的 MinIO Console。

      以下 location 块提供了模板，可在你的环境中进一步定制：

      .. code-block:: nginx
         :class: copyable

         upstream minio_s3 {
            least_conn;
            server minio-01.internal-domain.com:9000;
            server minio-02.internal-domain.com:9000;
            server minio-03.internal-domain.com:9000;
            server minio-04.internal-domain.com:9000;
         }

         upstream minio_console {
            least_conn;
            server minio-01.internal-domain.com:9001;
            server minio-02.internal-domain.com:9001;
            server minio-03.internal-domain.com:9001;
            server minio-04.internal-domain.com:9001;
         }

         server {
            listen       80;
            listen  [::]:80;
            server_name  minio.example.net;

            # Allow special characters in headers
            ignore_invalid_headers off;
            # Allow any size file to be uploaded.
            # Set to a value such as 1000m; to restrict file size to a specific value
            client_max_body_size 0;
            # Disable buffering
            proxy_buffering off;
            proxy_request_buffering off;

            location / {
               proxy_set_header Host $http_host;
               proxy_set_header X-Real-IP $remote_addr;
               proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
               proxy_set_header X-Forwarded-Proto $scheme;

               proxy_connect_timeout 300;
               # Default is HTTP/1, keepalive is only enabled in HTTP/1.1
               proxy_http_version 1.1;
               proxy_set_header Connection "";
               chunked_transfer_encoding off;

               proxy_pass https://minio_s3; # This uses the upstream directive definition to load balance
            }

            location /minio/ui/ {
               rewrite ^/minio/ui/(.*) /$1 break;
               proxy_set_header Host $http_host;
               proxy_set_header X-Real-IP $remote_addr;
               proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
               proxy_set_header X-Forwarded-Proto $scheme;
               proxy_set_header X-NginX-Proxy true;

               # This is necessary to pass the correct IP to be hashed
               real_ip_header X-Real-IP;

               proxy_connect_timeout 300;
               
               # To support websockets in MinIO versions released after January 2023
               proxy_http_version 1.1;
               proxy_set_header Upgrade $http_upgrade;
               proxy_set_header Connection "upgrade";
               # Some environments may encounter CORS errors (Kubernetes + Nginx Ingress)
               # Uncomment the following line to set the Origin request to an empty string
               # proxy_set_header Origin '';
               
               chunked_transfer_encoding off;

               proxy_pass https://minio_console; # This uses the upstream directive definition to load balance
            }
         }

      S3 API 签名计算算法*不*支持将 MinIO Server API 托管在 ``example.net/s3/`` 这类路径上的代理方案。

      你还必须为 MinIO 部署设置以下环境变量：

      - 将 :envvar:`MINIO_BROWSER_REDIRECT_URL` 设置为 MinIO Console 代理主机的 FQDN（``https://example.net/minio/ui``）

   .. tab-item:: 子域名

      为 MinIO Server S3 API 和 MinIO Console Web GUI 分别创建或配置独立且唯一的子域名。

      例如，给定根域 ``example.net``：

      - 将对 ``minio.example.net`` 子域名的请求代理到监听于 ``https://minio.local:9000`` 的 MinIO Server

      - 将对 ``console.example.net`` 子域名的请求代理到监听于 ``https://minio.local:9001`` 的 MinIO Console

      以下 location 块提供了模板，可在你的环境中进一步定制：

      .. code-block:: nginx
         :class: copyable

         upstream minio_s3 {
            least_conn;
            server minio-01.internal-domain.com:9000;
            server minio-02.internal-domain.com:9000;
            server minio-03.internal-domain.com:9000;
            server minio-04.internal-domain.com:9000;
         }

         upstream minio_console {
            least_conn;
            server minio-01.internal-domain.com:9001;
            server minio-02.internal-domain.com:9001;
            server minio-03.internal-domain.com:9001;
            server minio-04.internal-domain.com:9001;
         }

         server {
            listen       80;
            listen  [::]:80;
            server_name  minio.example.net;

            # Allow special characters in headers
            ignore_invalid_headers off;
            # Allow any size file to be uploaded.
            # Set to a value such as 1000m; to restrict file size to a specific value
            client_max_body_size 0;
            # Disable buffering
            proxy_buffering off;
            proxy_request_buffering off;

            location / {
               proxy_set_header Host $http_host;
               proxy_set_header X-Real-IP $remote_addr;
               proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
               proxy_set_header X-Forwarded-Proto $scheme;

               proxy_connect_timeout 300;
               # Default is HTTP/1, keepalive is only enabled in HTTP/1.1
               proxy_http_version 1.1;
               proxy_set_header Connection "";
               chunked_transfer_encoding off;

               proxy_pass http://minio_s3; # This uses the upstream directive definition to load balance
            }
         }

         server {

            listen       80;
            listen  [::]:80;
            server_name  console.example.net;

            # Allow special characters in headers
            ignore_invalid_headers off;
            # Allow any size file to be uploaded.
            # Set to a value such as 1000m; to restrict file size to a specific value
            client_max_body_size 0;
            # Disable buffering
            proxy_buffering off;
            proxy_request_buffering off;

            location / {
               proxy_set_header Host $http_host;
               proxy_set_header X-Real-IP $remote_addr;
               proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
               proxy_set_header X-Forwarded-Proto $scheme;
               proxy_set_header X-NginX-Proxy true;

               # This is necessary to pass the correct IP to be hashed
               real_ip_header X-Real-IP;

               proxy_connect_timeout 300;
               
               # To support websocket
               proxy_http_version 1.1;
               proxy_set_header Upgrade $http_upgrade;
               proxy_set_header Connection "upgrade";
               
               chunked_transfer_encoding off;

               proxy_pass http://minio_console/; # This uses the upstream directive definition to load balance
            }
         }

      S3 API 签名计算算法*不*支持将 MinIO Server API 托管在子路径上的代理方案，例如 ``minio.example.net/s3/``。

      你还必须为 MinIO 部署设置以下环境变量：

      - 将 :envvar:`MINIO_BROWSER_REDIRECT_URL` 设置为 MinIO Console 代理主机的 FQDN（``https://console.example.net/``）
