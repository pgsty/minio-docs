.. _minio-mc-encrypt-clear:

====================
``mc encrypt clear``
====================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc encrypt clear

语法
----

.. start-mc-encrypt-clear-desc

:mc:`mc encrypt clear` 命令用于移除存储桶当前的默认加密设置。

.. end-mc-encrypt-clear-desc

.. tab-set::

   .. tab-item:: 示例

      以下命令会移除 ``myminio`` :ref:`alias <alias>` 关联的 MinIO
      部署中 ``mydata`` 存储桶的默认加密设置：

      .. code-block:: shell
         :class: copyable

         mc encrypt clear myminio/mydata

   .. tab-item:: 语法

      该命令的语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] encrypt clear ALIAS

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~

.. mc-cmd:: ALIAS

   要移除默认 SSE 模式的目标存储桶完整路径。
   指定 MinIO 部署的 :ref:`alias <alias>` 作为 ALIAS 路径前缀。例如：

   .. code-block:: shell

      mc encrypt clear play/mybucket

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

移除存储桶的自动服务端加密设置
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tab-set::

   .. tab-item:: 示例

      .. code-block:: shell
         :class: copyable

          mc encrypt clear myminio/data

   .. tab-item:: 语法

      .. code-block:: shell
         :class: copyable

         mc encrypt clear ALIAS

      - 将 ``ALIAS`` 替换为目标 MinIO 部署的 :ref:`alias <alias>`，
        即要移除存储桶自动服务端加密配置的部署别名。

行为
----

修改存储桶加密设置不会影响已加密对象
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

禁用存储桶自动加密 *不会* 解密该存储桶中的任何对象。

要永久解密存储桶中的对象，可在禁用对象解密后执行原地复制。
对于启用版本控制的存储桶，旧版本对象仍保持加密状态。

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
