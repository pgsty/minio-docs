=============
``mc update``
=============

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 1

.. mc:: mc update

语法
------

.. start-mc-update-desc

:mc:`mc update` 命令会自动将 :program:`mc` 二进制更新到最新稳定版本。

.. end-mc-update-desc

运行此命令等同于手动下载最新稳定版二进制文件，并使用该文件替换主机上
现有的 ``mc`` 安装。

.. tab-set::

   .. tab-item:: 示例

      以下命令会更新本地主机上的 :program:`mc` 二进制文件：

      .. code-block:: shell
         :class: copyable

         mc update

   .. tab-item:: 语法

      该命令的语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] update

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

更新 :program:`minio` 服务器二进制后，请使用 :mc:`mc update`，
以确保行为一致性和兼容性。

全局参数
~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-json-globals
   :end-before: end-minio-mc-json-globals
