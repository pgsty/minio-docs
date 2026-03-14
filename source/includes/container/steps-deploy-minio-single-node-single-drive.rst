1) 拉取最新稳定版 MinIO 镜像
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. include:: /includes/container/common-deploy.rst
   :start-after: start-common-deploy-pull-latest-minio-image
   :end-before: end-common-deploy-pull-latest-minio-image

2) 创建环境变量文件
~~~~~~~~~~~~~~~~~~~

.. include:: /includes/common/common-deploy.rst
   :start-after: start-common-deploy-create-environment-file-single-drive 
   :end-before: end-common-deploy-create-environment-file-single-drive

.. include:: /includes/common/common-deploy.rst
   :start-after: start-common-deploy-create-unique-root-credentials 
   :end-before: end-common-deploy-create-unique-root-credentials

3) 创建并运行容器
~~~~~~~~~~~~~~~~~

选择你偏好的容器管理接口，以查看对应命令语法。

.. tab-set::

   .. tab-item:: Podman

      将命令复制到文本文件中，以便进一步修改。

      .. code-block:: shell
         :class: copyable

         podman run -dt                                  \
           -p 9000:9000 -p 9001:9001                     \
           -v PATH:/mnt/data                             \
           -v /etc/default/minio:/etc/config.env         \
           -e "MINIO_CONFIG_ENV_FILE=/etc/config.env"    \
           --name "minio_local"                          \
           minio server --console-address ":9001"

      根据本地环境需要，为 ``podman run`` 补充其他
      :podman-docs:`选项 <markdown/podman-run.1.html>`。

   .. tab-item:: Docker

      将命令复制到文本文件中，以便进一步修改。

      .. code-block:: shell
         :class: copyable

         docker run -dt                                  \
           -p 9000:9000 -p 9001:9001                     \
           -v PATH:/mnt/data                             \
           -v /etc/default/minio:/etc/config.env         \
           -e "MINIO_CONFIG_ENV_FILE=/etc/config.env"    \
           --name "minio_local"                          \
           minio server --console-address ":9001"

      根据本地环境需要，为 ``docker run`` 补充其他
      `选项 <https://docs.docker.com/engine/reference/commandline/run/>`__。

      如果以 Rootless 模式运行 Docker，
      你可能还需要设置以下额外 Docker CLI 选项：

      Linux
         ``--user $(id -u):$(id -g)`` - 指示容器以当前登录用户身份运行。
      
      Windows
         ``--security-opt "credentialspec=file://path/to/file.json"`` - 指示容器使用 Windows `Group Managed Service Account <https://docs.microsoft.com/en-us/virtualization/windowscontainers/manage-containers/manage-serviceaccounts>`_ 运行。

下表说明了命令各行的含义，并提供额外配置指引：

.. list-table::
   :header-rows: 1
   :widths: 40 60
   :width: 100%

   * - 行
     - 说明

   * - | ``podman run -dt``
       | ``docker run -dt``
     - 指示 Podman/Docker 以 detached（``-d``）后台进程并带 pseudo-TTY（``-t``）的方式创建并启动容器。
       这样容器就可以在后台运行，同时保留一个可供 bash 类交互访问的 TTY。

   * - ``-p 9000:9000 -p 9001:9001``
     - 将本机上的 ``9000`` 和 ``9001`` 端口绑定到容器中的同名端口。
       这样即可通过本地机器访问容器。

   * - ``-v PATH:/data/minio``
     - 将本机上的存储卷 ``PATH`` 绑定到容器中的 ``/data`` 路径。
       请将该值替换为本机上存储卷或文件夹的完整路径。
       例如：

       Linux 或 macOS
         ``~/minio/data/``
       
       Windows
         ``C:\minio\data``

   * - ``-v /etc/default/minio:/etc/config.env``
     - 将上一步创建的环境文件挂载到容器中的 ``/etc/config.env`` 路径。
       对于 Windows 主机，请指定 Windows 风格路径 ``-v C:\minio\config:/etc/config.env``。
         
       MinIO Server 使用该环境文件进行配置。
         
   * - ``-e "MINIO_CONFIG_ENV_FILE=/etc/config.env"``
     - 设置一个 MinIO 环境变量，指向容器内挂载后的环境文件路径。

   * - ``--name "minio_local"``
     - 为容器设置自定义名称。
       如果省略该值，Podman/Docker 会自动生成容器名。
       你可以按需替换为更合适的名称。

   * - ``minio server --console-address ":9001"``
     - 使用前一步拉取的 ``minio:minio`` 镜像启动 MinIO server。
       :mc-cmd:`minio server --console-address ":9001" <minio server --console-address>`
       选项会指示 server 为 MinIO Console Web Interface 设置固定端口。
       该选项在容器化环境中 *必需*。

       如果你修改该值，请确保同时通过 Podman/Docker 的 ``-p`` 参数设置正确的端口映射，
       以保证本地主机与容器之间的流量转发正常。

完成其他必要定制后，请在你偏好的 terminal 或 shell 环境中运行该命令。
命令应返回新建容器的唯一 ID。

4) 验证容器状态
~~~~~~~~~~~~~~

.. include:: /includes/container/common-deploy.rst
   :start-after: start-common-deploy-validate-container-status
   :end-before: end-common-deploy-validate-container-status

.. code-block:: shell

   Status:         1 Online, 0 Offline. 
   API: http://10.0.2.100:9000  http://127.0.0.1:9000       
   RootUser: myminioadmin 
   RootPass: minio-secret-key-change-me 
   Console: http://10.0.2.100:9001 http://127.0.0.1:9001    
   RootUser: myminioadmin 
   RootPass: minio-secret-key-change-me 

   Command-line: https://silo.pigsty.cc/reference/minio-mc.html
      $ mc alias set myminio http://10.0.2.100:9000 myminioadmin minio-secret-key-change-me

   Documentation: https://silo.pigsty.cc/operations/deployments/baremetal-deploy-minio-as-a-container.html

.. admonition:: 容器网络在主机外部可能不可访问

   ``API`` 和 ``CONSOLE`` 区块中可能包含容器的网络接口地址。
   容器网络之外的客户端无法通过这些地址访问 MinIO API 或 Console。

   如需外部访问，必须使用容器宿主机的网络地址，
   并且假定宿主机防火墙允许访问相关端口（示例中为 ``9000`` 和 ``9001``）。

5) 连接到 MinIO 服务
~~~~~~~~~~~~~~~~~~~~

.. include:: /includes/container/common-deploy.rst
   :start-after: start-common-deploy-connect-to-minio-service
   :end-before: end-common-deploy-connect-to-minio-service
