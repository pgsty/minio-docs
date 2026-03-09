=====================
``mc retention info``
=====================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc retention info

.. |command| replace:: :mc:`mc retention info`
.. |rewind| replace:: :mc-cmd:`~mc retention info --rewind`
.. |versionid| replace:: :mc-cmd:`~mc retention info --version-id`
.. |alias| replace:: :mc-cmd:`~mc retention info ALIAS`
.. |versions| replace:: :mc-cmd:`~mc retention info --versions`

语法
----

.. start-mc-retention-info-desc

:mc:`mc retention info` 命令用于为对象或存储桶中的对象配置 :ref:`Write-Once Read-Many (WORM)
locking <minio-object-locking>` 设置。
你还可以为存储桶设置默认对象锁设置，未显式配置对象锁的对象会继承该存储桶默认值。

.. end-mc-retention-info-desc

如需基于 :ref:`legal hold <minio-object-locking-legalhold>` 锁定对象，
请使用 :mc:`mc legalhold set`。

:mc:`mc retention info` *要求* 指定存储桶已启用对象锁定。
你**只能**在创建存储桶时启用对象锁定。有关创建启用对象锁定的存储桶，请参见
:mc-cmd:`mc mb --with-lock` 文档。

.. tab-set::

   .. tab-item:: 示例

      以下命令返回 ``myminio`` MinIO 部署中 ``mydata`` 存储桶的默认对象锁配置：

      .. code-block:: shell
         :class: copyable

         mc retention info --default myminio/mydata

   .. tab-item:: 语法

      命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] retention info            \
                          [--default]               \
                          [--recursive]             \
                          [--rewind "string"]       \
                          [--version-id "string"]*  \
                          [--versions]              \
                          ALIAS

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

      :mc-cmd:`mc retention info --version-id` 与多个其他参数互斥。
      更多信息请参见参考文档。

参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   要检索对象锁配置的对象完整路径。
   将已配置的 S3 兼容服务的 :ref:`alias <alias>` 作为 ``ALIAS`` 存储桶路径前缀。
   例如：

   .. code-block:: shell

      mc retention info play/mybucket/object.txt

   - 如果 ``ALIAS`` 指定的是存储桶或存储桶前缀，请添加
      :mc-cmd:`~mc retention info --recursive`，以返回该存储桶或前缀下所有对象的对象锁设置。

   - 如果 ``ALIAS`` 存储桶已启用版本控制，
      :mc:`mc retention info` 默认仅作用于对象的最新版本。
      使用 :mc-cmd:`~mc retention info --version-id` 或
      :mc-cmd:`~mc retention info --versions` 可返回特定版本或对象全部版本的对象锁设置。


.. mc-cmd:: --default
   :optional:

   返回 :mc-cmd:`~mc retention info ALIAS` 指定存储桶的默认对象锁设置。

   如果指定 :mc-cmd:`~mc retention info --default`，
   :mc:`mc retention info` 会忽略所有其他标志。

.. mc-cmd:: --recursive, r
   :optional:

   递归返回指定 :mc-cmd:`~mc retention info ALIAS` 路径下所有对象的对象锁设置。

   与 :mc-cmd:`~mc retention info --version-id` 互斥。

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

   与以下任一标志互斥：

   - :mc-cmd:`~mc retention info --versions`
   - :mc-cmd:`~mc retention info --rewind`
   - :mc-cmd:`~mc retention info --recursive`

.. mc-cmd:: --versions
   :optional:

   .. include:: /includes/facts-versioning.rst
      :start-after: start-versions-desc
      :end-before: end-versions-desc

   同时使用 :mc-cmd:`~mc retention info --versions` 和
   :mc-cmd:`~mc retention info --rewind`，可检索某个特定时间点存在的所有对象版本的
   保留设置。

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

检索单个或多个对象的对象锁设置
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tab-set::

   .. tab-item:: 指定对象

      .. code-block:: shell
         :class: copyable

         mc retention info ALIAS/PATH

      - 将 :mc-cmd:`ALIAS <mc retention info ALIAS>` 替换为已配置 S3 兼容主机的
        :mc:`alias <mc alias>`。

      - 将 :mc-cmd:`PATH <mc retention info ALIAS>` 替换为对象路径。

   .. tab-item:: 多个对象

      将 :mc:`mc retention info` 与
      :mc-cmd:`~mc retention info --recursive` 一起使用，以检索存储桶中所有对象的保留设置：

      .. code-block:: shell
         :class: copyable

         mc retention info --recursive ALIAS/PATH

      - 将 :mc-cmd:`ALIAS <mc retention info ALIAS>` 替换为已配置 S3 兼容主机的
        :mc:`alias <mc alias>`。

      - 将 :mc-cmd:`PATH <mc retention info ALIAS>` 替换为存储桶路径。

.. include:: /includes/facts-locking.rst
   :start-after: start-command-requires-locking-desc
   :end-before: end-command-requires-locking-desc

检索存储桶的默认对象锁设置
~~~~~~~~~~~~~~~~~~~~~~~~~~

将 :mc:`mc retention info` 与
:mc-cmd:`~mc retention info --default` 一起使用，以检索存储桶的默认对象锁设置：

.. code-block:: shell
   :class: copyable

   mc retention info --default ALIAS/PATH

- 将 :mc-cmd:`ALIAS <mc retention info ALIAS>` 替换为已配置 S3 兼容主机的
   :mc:`alias <mc alias>`。

- 将 :mc-cmd:`PATH <mc retention info ALIAS>` 替换为存储桶路径。

.. include:: /includes/facts-locking.rst
   :start-after: start-command-requires-locking-desc
   :end-before: end-command-requires-locking-desc

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
