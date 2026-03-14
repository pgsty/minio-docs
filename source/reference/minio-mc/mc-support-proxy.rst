====================
``mc support proxy``
====================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 1

.. mc:: mc support proxy

说明
----

.. start-mc-support-proxy-desc

使用 :mc-cmd:`mc support proxy` 命令配置与 |SUBNET| 通信时使用的代理。

.. end-mc-support-proxy-desc

示例
----

设置代理 URL
~~~~~~~~~~~~

定义部署 ``minio1`` 与 SUBNET 通信时使用的代理 URL。
此示例中的代理 URL 为 ``http://my.proxy``。

.. code-block:: shell
   :class: copyable
 
   mc support proxy set minio1 http://my.proxy

删除为部署配置的代理 URL
~~~~~~~~~~~~~~~~~~~~~~~~

以下命令会删除为别名 ``minio1`` 配置的代理 URL。

.. code-block:: shell
   :class: copyable
 
   mc support proxy remove minio1

禁用 ``callhome`` 日志
~~~~~~~~~~~~~~~~~~~~~~

以下命令显示为别名 ``minio1`` 配置的代理 URL。

.. code-block:: shell
   :class: copyable

   mc support proxy show minio1

语法
----

.. mc-cmd:: set
   :fullpath:

   为 MinIO 部署创建与 |SUBNET| 通信时使用的代理 URL。

   .. code-block:: shell
               
      mc support proxy set ALIAS PROXY_URL

.. mc-cmd:: show
   :fullpath:

   显示当前为与 |SUBNET| 通信配置的代理 URL。

   .. code-block:: shell
               
      mc support proxy show ALIAS

.. mc-cmd:: remove
   :fullpath:

   删除为与 |SUBNET| 通信配置的代理 URL。

   .. code-block:: shell
               
      mc support proxy remove ALIAS 

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals
