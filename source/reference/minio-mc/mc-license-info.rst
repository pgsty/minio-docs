===================
``mc license info``
===================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 1

.. mc:: mc license info

描述
----

.. start-mc-license-info-desc

:mc-cmd:`mc license info` 命令用于显示 MinIO 部署的许可证状态信息。
具体来说，用于确认该部署使用的是 AGPLv3 开源许可证，还是 `MinIO Commercial License <https://min.io/product/subnet?ref=docs>`__。

.. end-mc-license-info-desc

你必须在 MinIO |SUBNET| 中注册部署，才能激活商业许可证。

例如，对于未注册的部署，该命令会返回以下信息：

.. code-block:: shell

   You are using GNU AFFERO GENERAL PUBLIC LICENSE Version 3 (https://www.gnu.org/licenses/agpl-3.0.txt)

   If you are building proprietary applications, you may want to choose the commercial license 
   included as part of the Standard and Enterprise subscription plans. (https://min.io/signup?ref=mc)

   Applications must otherwise comply with all the GNU AGPLv3 License & Trademark obligations.

使用 :mc-cmd:`mc license register` 将你的部署关联到 SUBNET 账户。
如果你尚未注册 SUBNET，请参阅 `Registration <https://min.io/pricing?ref=docs>`__ 页面。

示例
----

显示别名为 ``minio1`` 的部署当前许可证
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. code-block:: shell
   :class: copyable

   mc license info minio1

如果部署使用的是已过期的 MinIO Commercial License，该命令会输出错误信息。

语法
----
      
该命令的语法如下：

.. code-block:: shell

   mc [GLOBALFLAGS] license info       \
                            ALIAS      \
                            [--airgap]

参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   MinIO 部署的 :ref:`alias <alias>`。
   
.. mc-cmd:: --airgap
   :optional:

   在运行 :ref:`minio client <minio-client>` 的客户端机器无法通过网络访问 SUBNET 的环境中使用（例如 airgapped、受防火墙限制或类似配置），以显示如何将部署注册到 SUBNET 的说明。
   
   如果部署处于 airgapped 环境，但本地设备具备网络访问能力，则无需使用 ``--airgap`` 标志。

全局选项
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals
