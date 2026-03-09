.. start-common-deploy-pull-latest-minio-image

选择 Podman 或 Docker 对应的选项卡，
查看如何拉取 MinIO 容器镜像。
说明中包含 quay.io 和 DockerHub 的示例：

.. tab-set::

   .. tab-item:: Podman

      quay.io
         .. code-block:: shell
            :class: copyable

            podman pull quay.io/minio/minio

      DockerHub
         .. code-block:: shell
            :class: copyable

            podman pull docker://minio/minio

   .. tab-item:: Docker

      quay.io
         .. code-block:: shell
            :class: copyable

            docker pull quay.io/minio/minio

      DockerHub
         .. code-block:: shell
            :class: copyable

            docker pull docker://minio/minio

.. end-common-deploy-pull-latest-minio-image

.. start-common-deploy-validate-container-status

.. tab-set::

   .. tab-item:: Podman

      运行以下命令获取容器日志。
      将容器名称替换为上一步 ``--name`` 指定的值。

      .. code-block:: shell
         :class: copyable

         podman logs minio

      该命令应返回类似如下的输出：

   .. tab-item:: Docker

      运行以下命令获取容器日志。
      将容器名称替换为上一步 ``--name`` 指定的值。

      .. code-block:: shell
         :class: copyable

         docker logs minio

      该命令应返回类似如下的输出：

.. end-common-deploy-validate-container-status

.. start-common-deploy-connect-to-minio-service

.. tab-set::

   .. tab-item:: MinIO Web Console

      可在浏览器中输入 http://localhost:9001 访问 MinIO Web Console。
      发往本地主机 MinIO Console 端口的任何流量都会被转发到该容器。

      使用容器指定环境文件中的 :envvar:`MINIO_ROOT_USER`
      和 :envvar:`MINIO_ROOT_PASSWORD` 登录。

      .. image:: /images/minio-console/console-bucket-none.png
         :width: 600px
         :alt: MinIO Console displaying Buckets view in a fresh installation.
         :align: center

      每个 MinIO 服务器都内置自己的 MinIO Console。

      如果本地主机防火墙允许外部访问 Console 端口，
      同一网络中的其他主机也可以通过本地主机的 IP 或主机名访问 Console。

   .. tab-item:: MinIO CLI (mc)

      可在 Terminal 或 Shell 中使用 :ref:`MinIO Client <minio-client>` (:mc:`mc`)
      访问 MinIO 部署。
      关于如何安装 :mc:`mc`，请参阅 :ref:`MinIO Client Installation Quickstart <mc-install>`。

      为该 MinIO 部署创建新的 :mc:`alias <mc alias set>`。
      使用本地主机的主机名或 IP 地址以及 S3 API 端口 ``9000``
      来访问该 MinIO 部署。
      发往本地主机该端口的任何流量都会被转发到该容器。

      .. code-block:: shell
         :class: copyable

         mc alias set minio-alias http://localhost:9000 myminioadmin minio-secret-key-change-me

      - 将 ``minio-alias`` 替换为要为该部署创建的别名名称。

      - 将 ``myminioadmin`` 和 ``minio-secret-key-change-me`` 替换为容器指定环境文件中的
        :envvar:`MINIO_ROOT_USER` 和 :envvar:`MINIO_ROOT_PASSWORD` 值。

      如果容器已运行且可通过指定端口访问，该命令应返回成功。

      之后即可使用任意 :mc:`mc` 命令与该容器交互。
      如果本地主机防火墙允许外部访问 MinIO S3 API 端口，
      同一网络中的其他主机也可以通过本地主机的 IP 或主机名访问该 MinIO 部署。

.. end-common-deploy-connect-to-minio-service

.. start-common-prereq-container-management-interface

本流程假定你已经安装了可正常工作的
`Podman <https://podman.io/getting-started/installation.html>`_，
并将其配置为以 “Rootfull” 模式运行。

“Rootless” 模式可能无法提供运行 KES 所需安全设置的足够权限。
更多信息请参阅相关 :podman-git:`“rootless” 文档 <blob/main/docs/tutorials/rootless_tutorial.md>`。

.. end-common-prereq-container-management-interface
