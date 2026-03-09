.. |KEYCLOAK_URL| replace:: localhost:8080
.. |MINIO_S3_URL| replace:: localhost:9000
.. |MINIO_CONSOLE_URL| replace:: localhost:9001

1) 创建 Podman Pod
~~~~~~~~~~~~~~~~~~~~~~~~

创建一个 Podman Pod，在共享网络的 Pod 中部署 Keycloak 和 MinIO 容器。
这样可以确保两个容器能够正常通信。

.. code-block:: shell
   :class: copyable

   podman pod create \ 
        -p 9000:9000 -p 9001:9001 -p 8080:8080 \
        -v ~/minio-keycloak/minio:/mnt/minio \
        -n minio-keycloak

将 ``~/minio-keycloak/minio`` 替换为一个空目录路径，
MinIO 容器会在其中存储数据。

你也可以选择以 root 身份部署这些容器，
以允许访问宿主机网络，从而实现容器间网络互通。

本教程不涵盖通过 Docker Compose 部署的内容。

2) 启动 Keycloak 容器
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

按照 `在容器中运行 Keycloak <https://www.keycloak.org/server/containers>`__ 的说明进行操作。
对于本流程，`以开发模式试用 Keycloak <https://www.keycloak.org/server/containers#_trying_keycloak_in_development_mode>`__
中的步骤已经足够。

.. code-block:: shell
   :class: copyable

   podman run -dt \
          --name keycloak \
          --pod minio-keycloak \
          -e KEYCLOAK_ADMIN=keycloakadmin \
          -e KEYCLOAK_ADMIN_PASSWORD=keycloakadmin123 \
          quay.io/keycloak/keycloak:latest start-dev

访问 ``localhost:8080`` 以打开 Keycloak 容器。

3) 配置或创建用于访问 Keycloak 的 Client
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

认证到 Keycloak :guilabel:`Administrative Console`，然后进入 :guilabel:`Clients`。

.. include:: /includes/common/common-configure-keycloak-identity-management.rst
   :start-after: start-configure-keycloak-client
   :end-before: end-configure-keycloak-client

4) 为 MinIO Client 创建 Client Scope
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Client scope 允许 Keycloak 在认证请求返回的 JSON Web Token（JWT）中映射用户属性。
这样 MinIO 就可以在向用户分配策略时引用这些属性。
这一步会创建在 Keycloak 认证成功后支持 MinIO 授权所需的 client scope。

.. include:: /includes/common/common-configure-keycloak-identity-management.rst
   :start-after: start-configure-keycloak-client-scope
   :end-before: end-configure-keycloak-client-scope

5) 将所需属性应用到 Keycloak Users/Groups
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

你必须为 Keycloak Users 或 Groups 分配一个名为 ``policy`` 的属性。
将其值设置为 MinIO 部署上的任意 :ref:`policy <minio-policy>`。

.. include:: /includes/common/common-configure-keycloak-identity-management.rst
   :start-after: start-configure-keycloak-user-group-attributes
   :end-before: end-configure-keycloak-user-group-attributes

6) 启动 MinIO 容器
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

以下命令会启动 MinIO 容器，并将其附加到 ``minio-keycloak`` pod。

.. code-block:: shell
   :class: copyable

   podman run -dt \
          --name minio-server \
          --pod minio-keycloak \
          quay.io/minio/minio:RELEASE.2023-02-22T18-23-45Z server /mnt/data --console-address :9001

访问 ``localhost:9001`` 打开 MinIO Console。
使用默认凭证 ``minioadmin:minioadmin`` 登录。

7) 为 Keycloak 认证配置 MinIO
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

MinIO 支持多种配置 Keycloak 认证的方法：

- 使用终端 / shell 以及 :mc:`mc idp openid` 命令
- 使用在启动 MinIO 之前设置的环境变量

.. tab-set::

   .. tab-item:: CLI

      .. include:: /includes/common/common-configure-keycloak-identity-management.rst
         :start-after: start-configure-keycloak-minio-cli
         :end-before: end-configure-keycloak-minio-cli

   .. tab-item:: 环境变量

      .. include:: /includes/common/common-configure-keycloak-identity-management.rst
         :start-after: start-configure-keycloak-minio-envvar
         :end-before: end-configure-keycloak-minio-envvar

你必须重启 MinIO 部署以应用这些更改。

检查 :ref:`MinIO server logs <minio-logging>`，
确认启动成功，且没有与 Keycloak 配置相关的错误。

8) 使用 Security Token Service（STS）生成应用程序凭证
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. include:: /includes/common/common-configure-keycloak-identity-management.rst
   :start-after: start-configure-keycloak-sts
   :end-before: end-configure-keycloak-sts

后续步骤
~~~~~~~~~~~~~

应用程序应使用其选择的 :ref:`SDK <minio-drivers>` 实现
:ref:`STS <minio-security-token-service>` 流程。
当 STS 凭证过期时，应用程序应具备在重试并继续操作之前重新生成 JWT token、
STS token 和 MinIO 凭证的逻辑。
