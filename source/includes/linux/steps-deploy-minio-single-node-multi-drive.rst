1) 下载 MinIO Server
~~~~~~~~~~~~~~~~~~~~

.. include:: /includes/linux/common-installation.rst
   :start-after: start-install-minio-binary-desc
   :end-before: end-install-minio-binary-desc

2) 创建 ``systemd`` 服务文件
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. include:: /includes/linux/common-installation.rst
   :start-after: start-install-minio-systemd-desc
   :end-before: end-install-minio-systemd-desc

3) 创建环境变量文件
~~~~~~~~~~~~~~~~~~~

.. include:: /includes/common/common-deploy.rst
   :start-after: start-common-deploy-create-environment-file-multi-drive 
   :end-before: end-common-deploy-create-environment-file-multi-drive

.. include:: /includes/common/common-deploy.rst
   :start-after: start-common-deploy-create-unique-root-credentials 
   :end-before: end-common-deploy-create-unique-root-credentials

4) 启动 MinIO 服务
~~~~~~~~~~~~~~~~~~

在本地主机上执行以下命令，以服务方式启动 MinIO |SNSD| 部署：

.. include:: /includes/linux/common-installation.rst
   :start-after: start-install-minio-start-service-desc
   :end-before: end-install-minio-start-service-desc

``journalctl`` 输出应类似如下：

.. code-block:: shell

   Status:         1 Online, 0 Offline. 
   API: http://192.168.2.100:9000  http://127.0.0.1:9000       
   RootUser: myminioadmin 
   RootPass: minio-secret-key-change-me 
   Console: http://192.168.2.100:9001 http://127.0.0.1:9001    
   RootUser: myminioadmin 
   RootPass: minio-secret-key-change-me 

   Command-line: https://silo.pigsty.cc/reference/minio-mc.html
      $ mc alias set myminio http://10.0.2.100:9000 myminioadmin minio-secret-key-change-me

   Documentation: https://silo.pigsty.cc/index.html

``API`` 区块列出了客户端可访问 MinIO S3 API 的网络接口和端口。
``Console`` 区块列出了客户端可访问 MinIO Web Console 的网络接口和端口。

5) 连接到 MinIO 服务
~~~~~~~~~~~~~~~~~~~~

.. include:: /includes/common/common-deploy.rst
   :start-after: start-common-deploy-connect-to-minio-deployment
   :end-before: end-common-deploy-connect-to-minio-deployment
