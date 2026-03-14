.. _minio-mc-legalhold-set:

====================
``mc legalhold set``
====================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc legalhold set

.. |command| replace:: :mc:`mc legalhold set`
.. |rewind| replace:: :mc-cmd:`~mc legalhold set --rewind`
.. |versionid| replace:: :mc-cmd:`~mc legalhold set --version-id`
.. |alias| replace:: :mc-cmd:`~mc legalhold set ALIAS`

语法
----

.. start-mc-legalhold-set-desc

:mc:`mc legalhold set` 命令可在单个或多个对象上启用
:ref:`legal hold <minio-object-locking-legalhold>` 的一次写入多次读取（WORM）对象锁定。

.. end-mc-legalhold-set-desc

:mc:`mc legalhold` *要求* 指定的存储桶已
:ref:`启用对象锁定 <minio-object-locking>`。你**只能**在创建存储桶时启用对象锁定。
有关创建已启用对象锁定的存储桶，请参见 :mc-cmd:`mc mb --with-lock` 文档。

.. tab-set::

   .. tab-item:: 示例

      以下命令会在 ``myminio`` MinIO 部署中，对 ``mydata`` 存储桶内所有现有对象启用
      legalhold WORM 锁定：

      .. code-block:: shell
         :class: copyable

         mc legalhold set --recursive myminio/mydata

   .. tab-item:: 语法

      该命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] legalhold set  \
                          [--recursive]  \
                          [--rewind]     \
                          [--version-id] \
                          ALIAS

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   MinIO :ref:`别名 <alias>`，以及要启用 legal hold 的对象路径（可为单个或多个对象）。
   例如：

   .. code-block:: shell
      
      mc legalhold set play/mybucket/myobjects/objects.txt

.. mc-cmd:: --recursive, r
   :optional:

   将 legal hold 应用于 :mc-cmd:`~mc legalhold set ALIAS` 指定存储桶或存储桶前缀下的
   所有现有对象。

   .. admonition:: ``--recursive`` 仅作用于现有对象
      :class: note

      若要为后续新建对象启用 legal hold，请在有新对象创建后周期性重复执行 :mc:`mc legalhold` 命令。

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

使用 :mc:`mc legalhold set` 为对象启用 legal hold：

.. code-block:: shell
   :class: copyable

   mc legalhold set [--recursive] ALIAS/PATH 

- 将 :mc-cmd:`ALIAS <mc legalhold set ALIAS>` 替换为 S3 兼容主机的
  :ref:`alias <alias>`。

- 将 :mc-cmd:`PATH <mc legalhold set ALIAS>` 替换为 S3 兼容主机上的存储桶或对象路径。
  如果指定的是存储桶或存储桶前缀路径，请包含
  :mc-cmd:`~mc legalhold set --recursive` 选项。

行为
----

Legal Hold 需要显式移除
~~~~~~~~~~~~~~~~~~~~~~~

Legal hold 没有期限，并会对被锁定对象强制执行完全不可变。
只有具备 :policy-action:`s3:PutObjectLegalHold` 权限的特权用户才能设置或解除 legal hold。

Legal Hold 与其他保留模式互补
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Legal hold 与 :ref:`minio-object-locking-governance` 和
:ref:`minio-object-locking-compliance` 保留设置互补。若对象同时受 legal hold 和
``GOVERNANCE/COMPLIANCE`` 保留规则约束，则在 legal hold 被解除且规则到期之前，
该对象会一直保持 WORM 锁定。

对于 ``GOVERNANCE`` 锁定对象，legal hold 会阻止对象被修改，
*即使*用户具备绕过保留策略所需的权限也是如此。

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
