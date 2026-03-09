=======================
``mc license register``
=======================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 1

.. mc:: mc support register
.. mc:: mc license register

.. important:: 

   ``mc license register`` 需要 :ref:`MinIO Client <minio-client>` ``RELEASE.2023-11-20T16-30-59Z`` 或更高版本。
   虽非强制要求，但最佳实践是保持 :ref:`MinIO Client 版本 <mc-client-versioning>` 与 MinIO Server 版本一致。


描述
----

.. start-mc-license-register-desc

:mc-cmd:`mc license register` 命令会将你的部署与 |SUBNET| 账户关联。

.. end-mc-license-register-desc

注册后，你可以使用 :mc-cmd:`mc support diag` 命令将部署健康报告直接上传到 SUBNET。

.. tab-set::

   .. tab-item:: 示例

         以下示例将 ``minio`` :ref:`alias <alias>` 注册到 |SUBNET|：

      .. code-block:: shell
         :class: copyable

         mc license register minio

   .. tab-item:: 语法

      命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] license register ALIAS                      \
                                  [--airgap]                          \
                                  [--api-key <string>]                \
                                  [--license <path to license file>]  \
                                  [--name <value>]

参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   MinIO 部署的 :ref:`alias <alias>`。

  
.. mc-cmd:: --airgap
   :optional:

   用于无法访问 SUBNET 网络的环境（例如 airgapped、受防火墙限制或类似配置）。

   说明请参见 :ref:`airgap 示例 <minio-license-register-airgap>`。

   如果部署处于 airgapped 环境，但你运行 :ref:`minio client <minio-client>` 的本地设备可以访问网络，则无需使用 ``--airgap`` 标志。

.. mc-cmd:: --api-key

   SUBNET 上账户的 API key。

   对应环境变量 ``MC_SUBNET_API_KEY``。

   获取 API key 的步骤：

   #. 登录 |SUBNET|
   #. 进入 :guilabel:`Deployments` 选项卡
   #. 在页面顶部、账户统计信息框右侧，选择 :guilabel:`API Key` 按钮
   #. 选择 key 字段右侧的复制按钮，将 key 值复制到剪贴板

.. mc-cmd:: --license
   :optional:

   用于注册部署的 license 文件路径。
   
   你必须先从 |SUBNET| 下载该账户的 license 文件。

   #. 登录 |SUBNET|
   #. 进入 :guilabel:`Deployments` 选项卡
   #. 在页面顶部、账户统计信息框右侧，选择 :guilabel:`License` 按钮
   #. 选择 license 字段右侧的复制按钮，将 key 值复制到剪贴板，或
      选择 :guilabel:`Download` 按钮将 license 的 txt 文件保存到本地

.. mc-cmd:: --name
   :optional:

   指定一个不同于 alias 的名称，用于在 SUBNET 中关联该 MinIO 部署。

   使用 ``--name <value>``，将 ``<value>`` 替换为你希望在 SUBNET 上为该部署使用的名称。

示例
----

使用部署名称注册部署
~~~~~~~~~~~~~~~~~~~~

将 alias 为 ``minio1`` 的 MinIO 部署注册到 SUBNET，并使用 ``minio1`` 作为部署名称：

.. code-block:: shell
   :class: copyable

   mc license register minio1

如果尚未注册，系统会提示输入该部署的 SUBNET 凭证。

使用账户的 License 文件注册部署
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

将 alias 为 ``minio5`` 的新 MinIO 部署注册到 SUBNET，并使用为该账户下载的 license 文件：

.. code-block:: shell
   :class: copyable

   mc license register minio5 /path/to/minio.license

如果尚未下载，你可以从 SUBNET 下载 license 文件。

#. 登录 |SUBNET|
#. 进入 :guilabel:`Deployments` 选项卡
#. 在页面顶部、账户统计信息框右侧，选择 :guilabel:`License` 按钮
#. 选择 :guilabel:`Download` 按钮将 license 的 txt 文件保存到本地


使用不同的部署名称注册部署
~~~~~~~~~~~~~~~~~~~~~~~~~~

将 alias 为 ``minio2`` 的 MinIO 部署注册到 SUBNET，并使用 ``second-deployment`` 作为名称：

.. code-block:: shell
   :class: copyable

   mc license register minio2 --name second-deployment

.. _minio-license-register-airgap:

在无法直接访问互联网时注册部署
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

将 alias 为 ``minio3`` 的 MinIO 部署注册到 SUBNET；该部署由于防火墙、airgap 或类似原因无法直接访问互联网。

.. versionchanged:: mc RELEASE.2022-07-29T19-17-16Z

   airgap 注册流程适用于 ``RELEASE.2022-07-29T19-17-16Z`` 或更高版本的 MinIO Client。
   早期版本的 MinIO Client 无法注册 airgapped 部署。

.. code-block:: shell
   :class: copyable

   mc license register minio3 --airgap

#. 运行命令，返回带有 token 的注册链接
#. 在 Web 浏览器中打开复制的注册链接，并登录 SUBNET
#. 选择部署 :guilabel:`License` 编号右侧的 :guilabel:`?` 按钮
#. 在弹窗中选择下载链接，并将 key 保存到你有访问权限的路径
#. 在命令行中运行以下命令
   
   .. code-block:: shell
      :class: copyable

      mc license update minio3 <path-to-file>

   将 ``<path-to-file>`` 替换为你从 SUBNET 下载的文件路径。

语法
----
      
命令语法如下：

.. code-block:: shell

   mc [GLOBALFLAGS] license register       \
                            ALIAS          \
                            [--name value] \
                            [--airgap]




全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

行为
----

自动更新 License
~~~~~~~~~~~~~~~~

.. versionadded:: RELEASE.2023-01-18T04-36-38Z

注册到 |SUBNET| 后，MinIO 会每月自动检查并更新 license。

在 airgapped 或其他服务器无法直接访问互联网的环境中，使用带文件路径的 :mc-cmd:`mc license update` 来更新注册。
