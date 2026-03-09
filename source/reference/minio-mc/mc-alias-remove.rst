.. _minio-mc-alias-remove:

===================
``mc alias remove``
===================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc alias remove

语法
----

.. start-mc-alias-remove-desc

:mc:`mc alias remove` 从本地 :program:`mc` 配置中移除一个已存在的别名。

.. end-mc-alias-remove-desc

.. tab-set::

   .. tab-item:: 示例

      以下命令会从主机上移除 MinIO 部署的 ``myminio`` :ref:`alias <alias>`：

      .. code-block:: shell
         :class: copyable

         mc alias remove myminio

   .. tab-item:: 语法

      :mc:`mc alias remove` 命令的语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] alias remove ALIAS

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~

.. mc-cmd:: ALIAS

   *必填* 要从本地 :program:`mc` 配置中移除的别名。

全局参数
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

从 ``mc`` 配置中移除别名
~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc:`mc alias remove` 从 :program:`mc` 配置中移除一个已存在的别名：

.. tab-set::

   .. tab-item:: 示例

      以下命令会移除 ``myminio`` 别名。

      .. code-block:: shell
         :class: copyable

         mc alias remove myminio

   .. tab-item:: 语法

      .. code-block:: shell
         :class: copyable

         mc alias remove ALIAS

      将 ``ALIAS`` 替换为要移除的别名名称。

行为
~~~~

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
