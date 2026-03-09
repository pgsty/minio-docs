.. _minio-mc-legalhold-clear:

======================
``mc legalhold clear``
======================

.. default-domain:: minio

.. contents:: Table of Contents
   :local:
   :depth: 2

.. mc:: mc legalhold clear

.. |command| replace:: :mc:`mc legalhold clear`
.. |rewind| replace:: :mc-cmd:`~mc legalhold clear --rewind`
.. |versionid| replace:: :mc-cmd:`~mc legalhold clear --version-id`
.. |alias| replace:: :mc-cmd:`~mc legalhold clear ALIAS`

语法
----

.. start-mc-legalhold-clear-desc

:mc:`mc legalhold clear` 命令会移除一个或多个对象当前的
:ref:`legal hold <minio-object-locking-legalhold>` 设置。

.. end-mc-legalhold-clear-desc

移除对象的 legal hold *不会* 移除对象上已有的其他
:ref:`minio-object-locking-governance` 和
:ref:`minio-object-locking-compliance` 保留设置。

.. tab-set::

   .. tab-item:: EXAMPLE

      以下命令会移除 ``myminio`` MinIO 部署中 ``mydata`` 存储桶内
      所有对象的 legal hold：

      .. code-block:: shell
         :class: copyable

         mc legalhold clear --recursive myminio/mydata

   .. tab-item:: SYNTAX

      该命令的语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] legalhold clear \
                          [--recursive]   \
                          [--rewind]      \
                          [--version-id]  \
                          ALIAS

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   MinIO :ref:`alias <alias>` 以及要移除 legal hold 的对象路径（可为单个或多个对象）。例如：

   .. code-block:: shell
      
      mc legalhold clear play/mybucket/myobjects/objects.txt

.. mc-cmd:: --recursive, r
   :optional:

   移除 :mc-cmd:`~mc legalhold clear ALIAS` 指定存储桶或存储桶前缀下
   所有对象的 legal hold。

.. mc-cmd:: --rewind
   :optional:

   .. include:: /includes/facts-versioning.rst
      :start-after: start-rewind-desc
      :end-before: end-rewind-desc

.. mc-cmd:: --version-id, vid
   :optional:

   .. include:: /includes/facts-versioning.rst
      :start-after: start-version-id-desc
      :end-before: end-version-id-desc

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

获取对象的 Legal Hold 状态
~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc:`mc legalhold clear` 获取对象的 legal hold 状态。
包含 :mc-cmd:`~mc legalhold clear --recursive` 可返回存储桶内容的
legal hold 状态：

.. code-block:: shell
   :class: copyable

   mc legalhold clear [--recursive] ALIAS/PATH

- 将 :mc-cmd:`ALIAS <mc legalhold clear ALIAS>` 替换为 S3 兼容主机的
  :ref:`alias <alias>`。

- 将 :mc-cmd:`PATH <mc legalhold clear ALIAS>` 替换为 S3 兼容主机上的
  存储桶或对象路径。若指定的是存储桶或存储桶前缀路径，请包含
  :mc-cmd:`~mc legalhold clear --recursive` 选项。

行为
----

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
