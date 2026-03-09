.. _minio-mc-alias-list:

=================
``mc alias list``
=================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc alias list

语法
------

.. start-mc-alias-list-desc

:mc:`mc alias list` 命令列出本地 :program:`mc` 配置中的所有别名。

.. end-mc-alias-list-desc

该命令输出包含每个别名关联的 access key 和 secret key 配置。

.. tab-set::

   .. tab-item:: 示例

      以下命令列出本地主机上配置的所有 :ref:`aliases <alias>`：

      .. code-block:: shell
         :class: copyable

         mc alias list

   .. tab-item:: 语法

      :mc:`mc alias list` 命令语法如下：

      .. code-block:: shell

         mc [GLOBALFLAGS] alias list [ALIAS]

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~~~~~~~

.. mc-cmd:: ALIAS

   *可选* 要显示的特定别名名称。

全局标志
~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
--------

列出所有已配置别名
~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tab-set::

   .. tab-item:: 示例

      以下 :mc:`mc alias list` 命令列出本地 :program:`mc` 配置中所有已配置的别名。

      .. code-block:: shell
         :class: copyable

         mc alias list

   .. tab-item:: 语法

      .. code-block:: shell
         :class: copyable

         mc alias list

列出特定别名
~~~~~~~~~~~~~~~~~~~~~

.. tab-set::

   .. tab-item:: 示例

      以下 :mc:`mc alias list` 命令列出本地 :program:`mc` 配置中特定别名的详细信息。

      .. code-block:: shell
         :class: copyable

         mc alias list myminio

   .. tab-item:: 语法

      .. code-block:: shell
         :class: copyable

         mc alias list ALIAS 

      - 将 ``ALIAS`` 替换为要返回的别名名称。

行为
--------

S3 兼容性
~~~~~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
