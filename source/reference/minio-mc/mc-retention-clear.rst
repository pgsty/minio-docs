======================
``mc retention clear``
======================

.. default-domain:: minio

.. contents:: Table of Contents
   :local:
   :depth: 2

.. mc:: mc retention clear

.. |command| replace:: :mc:`mc retention clear`
.. |rewind| replace:: :mc-cmd:`~mc retention clear --rewind`
.. |versionid| replace:: :mc-cmd:`~mc retention clear --version-id`
.. |versions| replace:: :mc-cmd:`~mc retention clear --versions`
.. |alias| replace:: :mc-cmd:`~mc retention clear ALIAS`

语法
----

.. start-mc-retention-clear-desc

:mc:`mc retention clear` 命令可移除存储桶中一个或多个对象的
:ref:`Write-Once Read-Many (WORM) locking <minio-object-locking>` 设置。
你还可以移除存储桶的默认对象锁设置。

.. end-mc-retention-clear-desc

要更改处于 :ref:`legal hold <minio-object-locking-legalhold>` 状态的对象的
保留状态，请使用 :mc:`mc legalhold clear`。

.. tab-set::

   .. tab-item:: 示例

      以下命令会移除 ``myminio`` MinIO 部署中 ``mydata`` 存储桶的默认对象锁配置：

      .. code-block:: shell
         :class: copyable

         mc retention clear --default myminio/mydata

   .. tab-item:: 语法

      该命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] retention clear           \
                          [--default]               \
                          [--recursive]             \
                          [--rewind "string"]       \
                          [--version-id "string"]*  \
                          [--versions]              \
                          ALIAS

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

      :mc-cmd:`mc retention clear --version-id` 与多个其他参数互斥。更多信息请参阅
      参考文档。

参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   要清除对象锁配置的对象或对象集合的完整路径。将已配置的 S3 兼容服务
   :mc-cmd:`alias <mc alias>` 作为 ``ALIAS`` 存储桶路径前缀。例如：

   .. code-block:: shell

      mc retention clear play/mybucket/object.txt

   - 如果 ``ALIAS`` 指定的是存储桶或存储桶前缀，请添加
      :mc-cmd:`~mc retention clear --recursive`，以清除存储桶内容的对象锁设置。

   - 如果 ``ALIAS`` 存储桶已启用版本控制，:mc:`mc retention clear` 默认仅作用于
      最新对象版本。使用 :mc-cmd:`~mc retention clear --version-id` 或
      :mc-cmd:`~mc retention clear --versions`，可清除某个特定版本或对象所有版本的
      对象锁设置。

.. mc-cmd:: --default
   :optional:

   清除 :mc-cmd:`~mc retention clear ALIAS` 指定存储桶的默认对象锁设置。
   
   如果指定 :mc-cmd:`~mc retention clear --default`，
   :mc:`mc retention clear` 会忽略所有其他 flag。

.. mc-cmd:: --recursive, r
   :optional:

   递归清除指定 :mc-cmd:`~mc retention clear ALIAS` 路径下所有对象的对象锁设置。

   与 :mc-cmd:`~mc retention clear --version-id` 互斥。

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

   与以下任一 flag 互斥：
   
   - :mc-cmd:`~mc retention clear --versions`
   - :mc-cmd:`~mc retention clear --rewind`
   - :mc-cmd:`~mc retention clear --recursive`

.. mc-cmd:: --versions
   :optional:   

   .. include:: /includes/facts-versioning.rst
      :start-after: start-versions-desc
      :end-before: end-versions-desc

   将 :mc-cmd:`~mc retention clear --versions` 与
   :mc-cmd:`~mc retention clear --rewind` 组合使用，可移除在特定时间点存在的
   所有对象版本的保留设置。

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

清除单个或多个对象的对象锁设置
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tab-set::

   .. tab-item:: 单个对象

      .. code-block:: shell
         :class: copyable

         mc retention clear ALIAS/PATH

      - 将 :mc-cmd:`ALIAS <mc retention clear ALIAS>` 替换为已配置的
        S3 兼容主机 :mc:`alias <mc alias>`。

      - 将 :mc-cmd:`PATH <mc retention clear ALIAS>` 替换为对象路径。


   .. tab-item:: 多个对象

      将 :mc:`mc retention clear` 与
      :mc-cmd:`~mc retention clear --recursive` 配合使用，可清除存储桶中所有对象的
      保留设置：

      .. code-block:: shell
         :class: copyable

         mc retention clear --recursive ALIAS/PATH

      - 将 :mc-cmd:`ALIAS <mc retention clear ALIAS>` 替换为已配置的
        S3 兼容主机 :mc:`alias <mc alias>`。

      - 将 :mc-cmd:`PATH <mc retention clear ALIAS>` 替换为存储桶路径。


.. include:: /includes/facts-locking.rst
   :start-after: start-command-requires-locking-desc
   :end-before: end-command-requires-locking-desc

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibilit
