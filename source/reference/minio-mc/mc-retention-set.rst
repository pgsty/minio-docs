.. _minio-bucket-locking:

====================
``mc retention set``
====================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc retention set

.. replacements for mc retention set

.. |command| replace:: :mc:`mc retention set`
.. |rewind| replace:: :mc-cmd:`~mc retention set --rewind`
.. |versionid| replace:: :mc-cmd:`~mc retention set --version-id`
.. |alias| replace:: :mc-cmd:`~mc retention set ALIAS`
.. |versions| replace:: :mc-cmd:`~mc retention set --versions`

语法
------

.. start-mc-retention-set-desc

:mc:`mc retention set` 命令用于为存储桶中的一个或多个对象配置
:ref:`Write-Once Read-Many (WORM) locking <minio-object-locking>` 设置。
你还可以为存储桶设置默认对象锁设置，使未显式配置对象锁的所有对象继承该存储桶默认值。

.. end-mc-retention-set-desc

要在 :ref:`legal hold <minio-object-locking-legalhold>` 下锁定对象，
请使用 :mc:`mc legalhold set`。

:mc:`mc retention set` *要求* 指定存储桶已启用对象锁。
你**只能**在创建存储桶时启用对象锁。有关启用对象锁创建存储桶的文档，请参见
:mc-cmd:`mc mb --with-lock`。

.. tab-set::

   .. tab-item:: 示例

      以下命令会在 ``myminio`` MinIO 部署中的 ``mydata`` 存储桶上，
      设置默认 30 天的 :ref:`GOVERNANCE <minio-object-locking-governance>` 对象锁：

      .. code-block:: shell
         :class: copyable

         mc retention set --default GOVERNANCE "30d" myminio/mydata

   .. tab-item:: 语法

      命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] retention set                         \
                          [--bypass]                            \
                          [--default]                           \
                          [--recursive]                         \
                          [--rewind "string"]                   \
                          [--versions]                          \
                          [--version-id "string"]*              \
                          MODE                                  \
                          "VALIDITY"                            \
                          ALIAS

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

      :mc-cmd:`mc retention set --version-id` 与多个其他参数互斥。
      更多信息请参见参考文档。

参数
~~~~~~~~~~

.. mc-cmd:: MODE
   :required:

   设置 :mc-cmd:`~mc retention set ALIAS` 的锁定模式。
   指定以下支持值之一：

   - ``governance``
   - ``compliance``

   有关支持模式的更多信息，请参见 AWS S3 文档
   :s3-docs:`Object Lock Overview
   <object-lock-overview.html>`。

   需要指定 :mc-cmd:`~mc retention set VALIDITY`。

.. mc-cmd:: VALIDITY
   :required:

   指定对象在创建后保持在
   :mc-cmd:`~mc retention set MODE` 中的持续时间。

   - 按天计时，指定格式为 ``Nd`` 的字符串。例如，
      ``30d`` 表示对象创建后 30 天。

   - 按年计时，指定格式为 ``Ny`` 的字符串。例如，
      ``1y`` 表示对象创建后 1 年。

.. mc-cmd:: ALIAS
   :required:

   需要设置对象锁配置的对象或对象集合的完整路径。
   指定 MinIO 或兼容 S3 服务的 :ref:`alias <alias>` 以及存储桶完整路径。
   例如：

   .. code-block:: shell

      mc retention set play/mybucket/object.txt MODE VALIDITY

   - 如果 ``ALIAS`` 指定的是存储桶或存储桶前缀，请包含
     :mc-cmd:`~mc retention set --recursive` 以将对象锁设置应用到
     存储桶内容。

   - :mc:`mc retention set` 默认仅应用到对象的最新版本。
     使用 :mc-cmd:`~mc retention set --version-id` 或
     :mc-cmd:`~mc retention set --versions` 可分别将对象锁设置
     应用于指定版本或对象的所有版本。

.. mc-cmd:: --bypass
   :optional:

   允许具有 ``s3:BypassGovernanceRetention`` 权限的用户
   修改对象。需要 ``governance`` 保留
   :mc-cmd:`~mc retention set MODE`

.. mc-cmd:: --default
   :optional:

   使用 :mc-cmd:`~mc retention set MODE` 和 :mc-cmd:`~mc retention set VALIDITY`，
   为 :mc-cmd:`~mc retention set ALIAS` 指定的存储桶设置默认对象锁设置。
   在该存储桶中创建的任何对象都会继承默认对象锁设置，
   除非使用 :mc:`mc retention set` 显式覆盖。

   如果指定 :mc-cmd:`~mc retention set --default`，
   :mc:`mc retention set` 会忽略所有其他标志。

.. mc-cmd:: --recursive
   :optional:
   :alias: --r

   递归将对象锁设置应用到
   :mc-cmd:`~mc retention set ALIAS` 路径下的所有对象。

   与 :mc-cmd:`~mc retention set --version-id` 互斥。

.. mc-cmd:: --rewind
   :optional:

   .. include:: /includes/facts-versioning.rst
      :start-after: start-rewind-desc
      :end-before: end-rewind-desc

.. mc-cmd:: --version-id
   :optional:
   :alias: --vid


   .. include:: /includes/facts-versioning.rst
      :start-after: start-version-id-desc
      :end-before: end-version-id-desc

   与以下任一标志互斥：

   - :mc-cmd:`~mc retention set --versions`
   - :mc-cmd:`~mc retention set --rewind`
   - :mc-cmd:`~mc retention set --recursive`

.. mc-cmd:: --versions
   :optional:

   .. include:: /includes/facts-versioning.rst
      :start-after: start-versions-desc
      :end-before: end-versions-desc

   将 :mc-cmd:`~mc retention set --versions` 与
   :mc-cmd:`~mc retention set --rewind` 结合使用，
   可将保留设置应用到特定时间点存在的所有对象版本。

全局标志
~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
--------

设置存储桶默认保留设置
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

将 :mc:`mc retention set` 与
:mc-cmd:`~mc retention set --recursive` 和
:mc-cmd:`~mc retention set --default` 结合使用，
以设置存储桶默认保留设置。

.. code-block:: shell
   :class: copyable

   mc retention set  --recursive --default MODE DURATION ALIAS/PATH

- 将 :mc-cmd:`MODE <mc retention set MODE>` 替换为要启用的保留模式。
  MinIO 支持 AWS S3 保留模式 ``governance`` 和 ``compliance``。

- 将 :mc-cmd:`DURATION <mc retention set VALIDITY>` 替换为对象锁应保持生效的时长。
  例如，要将保留期设置为 30 天，请指定 ``30d``。

- 将 :mc-cmd:`ALIAS <mc retention set ALIAS>` 替换为已配置的兼容 S3 主机的
  :mc:`alias <mc alias>`。

- 将 :mc-cmd:`PATH <mc retention set ALIAS>` 替换为存储桶路径。

.. include:: /includes/facts-locking.rst
   :start-after: start-command-requires-locking-desc
   :end-before: end-command-requires-locking-desc

为已版本化对象设置对象锁配置
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tab-set::

   .. tab-item:: 特定版本

      将 :mc:`mc retention set` 与
      :mc-cmd:`~mc retention set --version-id` 结合使用，
      可将保留设置应用到特定对象版本：

      .. code-block:: shell
         :class: copyable

         mc retention set --version-id VERSION MODE DURATION ALIAS/PATH

      - 将 :mc-cmd:`VERSION <mc retention set --version-id>` 替换为对象版本。

      - 将 :mc-cmd:`MODE <mc retention set MODE>` 替换为要启用的保留模式。
        MinIO 支持 AWS S3 保留模式 ``governance`` 和 ``compliance``。

      - 将 :mc-cmd:`DURATION <mc retention set VALIDITY>` 替换为对象锁应保持生效的时长。
        例如，要将保留期设置为 30 天，请指定 ``30d``。

      - 将 :mc-cmd:`ALIAS <mc retention set ALIAS>` 替换为已配置的兼容 S3 主机的
        :mc:`alias <mc alias>`。

      - 将 :mc-cmd:`PATH <mc retention set ALIAS>` 替换为对象路径。

   .. tab-item:: 所有版本

      将 :mc:`mc retention set` 与
      :mc-cmd:`~mc retention set --versions` 结合使用，
      可将保留设置应用到特定对象版本：

      .. code-block:: shell
         :class: copyable

         mc retention set --versions  MODE DURATION ALIAS/PATH

      - 将 :mc-cmd:`MODE <mc retention set MODE>` 替换为要启用的保留模式。
        MinIO 支持 AWS S3 保留模式 ``governance`` 和 ``compliance``。

      - 将 :mc-cmd:`DURATION <mc retention set VALIDITY>` 替换为对象锁应保持生效的时长。
        例如，要将保留期设置为 30 天，请指定 ``30d``。


      - 将 :mc-cmd:`ALIAS <mc retention set ALIAS>` 替换为已配置的兼容 S3 主机的
        :mc:`alias <mc alias>`。

      - 将 :mc-cmd:`PATH <mc retention set ALIAS>` 替换为对象路径。


.. include:: /includes/facts-locking.rst
   :start-after: start-command-requires-locking-desc
   :end-before: end-command-requires-locking-desc

行为
--------

对象版本的保留
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

对于启用了 :mc:`versioning <mc version>` 的存储桶，:mc:`mc retention set`
默认对目标对象（一个或多个）的*最新*版本执行操作。
:mc:`mc retention set` 包含若干特定选项，在*显式*指定时，
可指示命令对特定对象版本*或*对象的所有版本执行操作：

.. tab-set::

   .. tab-item:: 特定对象版本

      要让 :mc:`mc retention set` 对对象的特定版本执行操作，
      请包含 ``--version-id`` 参数：

      - :mc-cmd:`mc retention set --version-id`
      - :mc-cmd:`mc retention set --version-id`
      - :mc-cmd:`mc retention set --version-id`

   .. tab-item:: 所有对象版本

      要让 :mc:`mc retention set` 对对象的*所有*版本执行操作，
      请包含 ``--versions`` 参数：

      - :mc-cmd:`mc retention set --versions`
      - :mc-cmd:`mc retention set --versions`
      - :mc-cmd:`mc retention set --versions`

与 legal hold 的交互
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

锁定对象会阻止对该对象进行任何修改或删除，
与 :mc-cmd:`COMPLIANCE <mc retention set MODE>` 对象锁模式类似。
对象可以同时具有基于保留的锁和 legal hold 锁。

legal hold 锁会*覆盖*任何保留锁定，这意味着处于 legal hold 下的对象
即使保留期到期也会保持锁定。对处于 legal hold 下对象的保留设置进行设置、
修改或清除，在 legal hold 到期或被显式禁用之前均不会生效。

有关对象 legal hold 的更多信息，请参见 :mc:`mc legalhold`。

S3 兼容性
~~~~~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
