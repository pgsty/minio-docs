===================
``mc admin update``
===================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 1

.. mc:: mc admin update

描述
----

.. start-mc-admin-update-desc

:mc-cmd:`mc admin update` 命令会更新部署中的所有 MinIO 服务器。
该命令还支持使用私有镜像服务器，适用于部署环境无法访问公网的场景。

.. end-mc-admin-update-desc

运行该命令后，会显示确认更新的提示。
输入 ``y`` 并按 ``[ENTER]``，即可确认并继续更新。

用户**必须**对二进制安装目标位置具有 ``write`` 权限。

.. admonition:: 仅在 MinIO 部署上使用 ``mc admin``
   :class: note

   .. include:: /includes/facts-mc-admin.rst
      :start-after: start-minio-only
      :end-before: end-minio-only


注意事项
--------

更新无中断
~~~~~~~~~~

:mc-cmd:`mc admin update` 会更新二进制，并同时重启部署中的所有 MinIO 服务器。
MinIO 操作具备原子性和严格一致性，因此该重启过程不会中断应用。

MinIO 强烈建议仅执行同时升级并重启的流程。
请勿执行“滚动”（即一次一个节点）升级流程。

权限
~~~~

执行该命令的用户**必须**对 MinIO Server 二进制安装目标路径具有 ``write`` 权限。

示例
----

使用 :mc-cmd:`mc admin update` 更新 MinIO 部署中的每个 :mc:`minio` 服务器进程：

.. code-block:: shell
   :class: copyable

   mc admin update ALIAS

将 :mc-cmd:`ALIAS <mc admin update ALIAS>` 替换为该 MinIO 部署的 :mc-cmd:`alias <mc alias>`。

运行该命令后，在提示中输入 yes 以确认并执行更新。

语法
----

:mc-cmd:`mc admin update` 语法如下：

.. code-block:: shell
   :class: copyable

   mc admin update ALIAS         \
                   [MIRROR_URL]  \
                   [--yes]             

:mc-cmd:`mc admin update` 支持以下参数：

.. mc-cmd:: ALIAS

   要更新的 MinIO 部署的 :mc-cmd:`alias <mc alias>`。

   如果指定的 ``ALIAS`` 对应的是分布式 MinIO 部署，:mc-cmd:`mc admin update` 会同时更新该部署中的*所有* MinIO 服务器。

   使用 :mc:`mc alias list` 查看已配置的别名及其对应的 MinIO 部署端点。

.. mc-cmd:: MIRROR_URL
   
   ``minio`` server 二进制的镜像 URL，用于更新 :mc-cmd:`~mc admin update ALIAS` 部署中的 MinIO 服务器。

.. mc-cmd:: --yes, -y
   :optional:

   传入此标志以确认更新，并跳过确认提示。

行为
----

二进制压缩
~~~~~~~~~~

.. versionchanged:: RELEASE.2024-01-28T22-35-53Z

   :mc-cmd:`mc admin update` 会先压缩二进制，再发送到部署中的所有节点。

此功能不适用于 :ref:`systemctl managed deployments <minio-baremetal>`。
