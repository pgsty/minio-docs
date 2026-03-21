=====================
``mc license update``
=====================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 1

.. mc:: mc license update

描述
----

.. start-mc-license-update-desc

使用 :mc-cmd:`mc license update` 命令为部署替换许可证密钥。

.. end-mc-license-update-desc

对于已在 |SUBNET| 注册的部署，MinIO 每月会自动检查并更新许可证。

示例
----

更新别名为 ``minio1`` 的部署许可证密钥
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: shell
   :class: copyable

   mc license update minio1 license.key

语法
----
      
该命令具有以下语法：

.. code-block:: shell

   mc [GLOBALFLAGS] license update                   \
                            ALIAS                    \
                            [LICENSE-FILE-WITH-PATH] \
                            [--airgap]

参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   MinIO 部署的 :ref:`alias <alias>`。

.. mc-cmd:: LICENSE-FILE-WITH-PATH
   :optional:

   用于更新部署许可证的密钥文件路径（相对于当前工作目录）和文件名。

   从 SUBNET 下载 API key：

   #. 登录 |SUBNET|
   #. 转到 :guilabel:`Deployments` 选项卡
   #. 在页面顶部、账户统计信息框右侧，选择 :guilabel:`API Key` 按钮
   #. 选择 key 字段右侧的复制按钮，将 key 值复制到剪贴板

   
.. mc-cmd:: --airgap
   :optional:

   在无法通过网络访问 SUBNET 的环境中使用（例如 airgapped、firewalled 或类似配置）。

   如果部署是 airgapped，但你使用 :ref:`minio client <minio-client>` 的本地设备有网络访问能力，则无需使用 ``--airgap`` 标志。

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals
