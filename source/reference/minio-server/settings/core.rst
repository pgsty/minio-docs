.. _minio-server-envvar-core:

=============
核心设置
=============

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

本页介绍用于控制 MinIO 进程核心行为的设置。

.. include:: /includes/common-mc-admin-config.rst
   :start-after: start-minio-settings-defined
   :end-before: end-minio-settings-defined

.. include:: /includes/common-mc-admin-config.rst
   :start-after: start-minio-settings-test-before-prod
   :end-before: end-minio-settings-test-before-prod

MinIO Server CLI 选项
------------------------

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_OPTS

   .. tab-item:: 配置项
      :sync: config

      此变量没有对应的配置项，因为这些设置在服务器启动时生效。

*可选*

设置一个 :ref:`参数 <minio-server-parameters>` 字符串，在启动 MinIO Server 时使用。

对于采用推荐 MinIO ``systemd`` 服务的类 Unix 系统，请使用 ``/etc/default/minio`` 文件并创建环境变量 ``MINIO_OPTS``，用于指定要附加到 ``minio`` systemd 进程的参数：

.. code-block:: shell
   :class: copyable
   
   # Editing /etc/default/minio

   MINIO_OPTS=' --console-address=":9001" --ftp="address=:8021" --ftp="passive-port-range=30000-40000" '

对于在命令行运行 ``minio`` 的系统，``MINIO_OPTS`` 是可选项。
如需使用，请按标准 shell 语义声明该环境变量，然后在启动 MinIO Server 时引用该环境变量：

.. code-block:: shell
   :class: copyable

   export MINIO_OPTS=' --console-address=":9001" --ftp="address=:8021" --ftp="passive-port-range=30000-40000" '

   minio server $MINIO_OPTS ...

   # The above is equivalent to running the following:
   # minio server --console-address=":9001" \
   #              --ftp="address=:8021"     \
   #              --ftp="passive-port-range=30000-40000"

.. important::

   ``minio server`` 命令不会直接读取 ``$MINIO_OPTS``。
   该变量仅在按上述方式使用时才会生效。

存储卷
---------------

.. tab-set::

   .. tab-item:: 环境变量

      .. envvar:: MINIO_VOLUMES

         :mc:`minio server` 进程用作存储后端的目录或磁盘。

         在功能上等价于设置 :mc-cmd:`minio server DIRECTORIES`。
         在通过环境文件配置 MinIO 运行时使用此值。

   .. tab-item:: 配置项

      .. include:: /includes/common-mc-admin-config.rst
         :start-after: start-minio-settings-no-config-option
         :end-before: end-minio-settings-no-config-option

环境变量文件路径
------------------------------

.. tab-set::

   .. tab-item:: 环境变量

      .. envvar:: MINIO_CONFIG_ENV_FILE

         指定 MinIO server 进程用于加载环境变量的文件完整路径。

         对于由 ``systemd`` 管理的文件，将该值设置为环境文件路径（``/etc/default/minio``），以便在使用 :mc-cmd:`mc admin service restart` 重启部署时，指示 MinIO 重新加载该文件中的变更。

   .. tab-item:: 配置项

      .. include:: /includes/common-mc-admin-config.rst
         :start-after: start-minio-settings-no-config-option
         :end-before: end-minio-settings-no-config-option

过期处理工作线程
----------------------

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_ILM_EXPIRY_WORKERS

         指定用于处理按 ILM 过期规则配置对象过期任务的工作线程数。
         未设置时，MinIO 默认最多使用可用处理器核心数的一半。

   .. tab-item:: 配置项
      :sync: config

      .. include:: /includes/common-mc-admin-config.rst
         :start-after: start-minio-settings-no-config-option
         :end-before: end-minio-settings-no-config-option

域名
------

.. tab-set::

   .. tab-item:: 环境变量

      .. envvar:: MINIO_DOMAIN

         为 MinIO 部署启用 Virtual Host 风格请求。
         将该值设置为完全限定域名（FQDN），使 MinIO 能够接受传入的虚拟主机请求。

         省略此设置时，MinIO 仅接受默认的 path-style 请求。

         例如，某个 MinIO 部署分配的 FQDN 为 ``minio.example.net``。

         - 使用 path-style 访问时，应用可通过完整路径 ``minio.example.net/mybucket`` 访问存储桶。
         - 使用 virtual-host 访问时，应用可通过虚拟主机 ``mybucket.minio.example.net/`` 访问存储桶。

         .. important::

            如果配置了 ``MINIO_DOMAIN``，你**必须**将指定 FQDN 的所有子域名视为专用于存储桶名称。
            任何与这些域名冲突的 MinIO 服务（例如复制目标）都可能因为冲突而表现出意外或非预期行为。

            例如，若设置 ``MINIO_DOMAIN=minio.example.net``，则**不能**将 ``minio.example.net`` 的任何子域名（即 ``*.minio.example.net`` 形式）分配给任何 MinIO 服务或目标。
            这包括用于 :ref:`bucket <minio-bucket-replication>`、:ref:`batch <minio-batch-framework-replicate-job>` 或 :ref:`site replication <minio-site-replication-overview>` 的主机名。

   .. tab-item:: 配置项

      .. include:: /includes/common-mc-admin-config.rst
         :start-after: start-minio-settings-no-config-option
         :end-before: end-minio-settings-no-config-option

.. _minio-scanner-speed-options:

扫描器速度
-------------

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_SCANNER_SPEED

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: scanner speed
         :delimiter: " "

用于在平衡 MinIO 读写性能与扫描进程时，管理 :ref:`scanner <minio-concepts-scanner>` 的最大等待周期。

.. include:: /includes/common/scanner.rst
   :start-after: start-scanner-speed-values
   :end-before: end-scanner-speed-values

批量复制
-----------------

.. tab-set::

   .. tab-item:: 配置项

      .. include:: /includes/common-mc-admin-config.rst
         :start-after: start-minio-settings-no-config-option
         :end-before: end-minio-settings-no-config-option


数据压缩
----------------

以下部分记录用于为对象启用数据压缩的设置。
有关如何使用这些配置设置的教程，请参见 :ref:`minio-data-compression`。

本节中的所有设置都归属于以下顶层键：

.. mc-conf:: compression

启用压缩
~~~~~~~~~~~~~~~~~~

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_COMPRESSION_ENABLE

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: compression enable
         :delimiter: " "

*可选*

设置为 ``on`` 以对新对象启用数据压缩。
默认为 ``off``。

启用或禁用数据压缩不会更改现有对象。

允许加密
~~~~~~~~~~~~~~~~

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_COMPRESSION_ALLOW_ENCRYPTION

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: compression allow_encryption
         :delimiter: " "

*可选*

设置为 ``on`` 以在压缩后对对象进行加密。
默认为 ``off``。

.. admonition:: 对压缩对象加密可能会削弱安全性
   :class: warning

   MinIO 强烈不建议对压缩对象进行加密。
   如果你需要加密，请仔细评估可能泄露加密对象内容信息的风险。

压缩扩展名
~~~~~~~~~~~~~~~~~~~~~~

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_COMPRESSION_EXTENSIONS

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: compression extensions
         :delimiter: " "

*可选*

要压缩的文件扩展名列表，以逗号分隔。
设置新的扩展名列表会替换先前已配置的列表。
默认为 ``".txt, .log, .csv, .json, .tar, .xml, .bin"``。

.. versionchanged:: RELEASE.2024-03-15T01-07-19Z

   可指定 ``"*"`` 以指示 MinIO 压缩所有受支持的文件类型。

即使在此参数中显式指定，MinIO 也不支持压缩 :ref:`Excluded File Types <minio-data-compression-excluded-types>` 列表中的文件类型。

压缩 MIME 类型
~~~~~~~~~~~~~~~~~~~~~~

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_COMPRESSION_MIME_TYPES

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: compression mime_types
         :delimiter: " "

*可选*

要压缩的 MIME 类型列表，以逗号分隔。
设置新的类型列表会替换先前已配置的列表。
默认为 ``"text/*, application/json, application/xml, binary/octet-stream"``。

.. admonition:: 默认排除文件
   :class: note

   某些类型的文件无法显著减小体积。
   MinIO *不会* 压缩这些文件，即使它们在 :mc-conf:`~compression.mime_types` 参数中被指定。
   详见 :ref:`Excluded types <minio-data-compression-excluded-types>`。

注释
~~~~~~~~

.. tab-set::

   .. tab-item:: 环境变量

      此设置没有环境变量选项。
      请改用配置项。

   .. tab-item:: 配置项
      :selected:

      .. envvar:: compression comment

*可选*

指定一个与数据压缩配置关联的注释。

纠删码条带大小
-------------------

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_ERASURE_SET_DRIVE_COUNT

   .. tab-item:: 配置项
      :sync: config

      .. include:: /includes/common-mc-admin-config.rst
         :start-after: start-minio-settings-no-config-option
         :end-before: end-minio-settings-no-config-option

*可选*

应用于指定 :term:`服务器池` 中所有驱动器的 :ref:`erasure set size <minio-ec-basics>`。

如果设置此值，你**必须**在初始化集群*之前*完成设置。
集群初始化后，所选条带大小为**不可变**，并会影响后续添加到集群中的所有 服务器池。

|subnet| 用户应先登录并提交 issue，讨论条带大小设置后再在任何环境中实施。

.. warning::

   除非 MinIO 工程团队明确指导，否则**不要**更改条带大小设置。

   条带大小变更会对部署功能、可用性、性能和行为产生重大影响。
   MinIO 的条带选择算法已为大多数工作负载设置了适当默认值。
   偏离该默认值来调整条带大小并不常见，通常也没有必要或不被建议。

最大对象版本数
-----------------------

.. tab-set::

   .. tab-item:: 环境变量
      :sync: envvar

      .. envvar:: MINIO_API_OBJECT_MAX_VERSIONS

   .. tab-item:: 配置项
      :sync: config

      .. mc-conf:: api object_max_versions
         :delimiter: " "

*可选*

定义每个对象允许的默认最大版本数。

默认情况下，MinIO 允许每个对象的版本数最高达到 Int64 的最大值，即超过 9.2 quintillion。

.. note::

   ``RELEASE.2023-08-04T17-40-21Z`` 到 ``RELEASE.2024-03-26T22-10-45Z`` 之间的 MinIO 版本，默认上限为 10,000 个对象版本。
   可使用此设置将该上限覆盖为其他值。

为对象设置任意高的版本数可能导致某些操作（如 ``LIST``）性能下降。
在使用低成本硬件或机械硬盘（HDD）的系统上，这一点尤为明显。
对于每个对象会生成数千个或更多版本的应用或工作负载，可能需要进行设计或架构评审，以缓解潜在的性能下降。

将上限设置为不超过 ``100``，通常可满足大多数常见使用场景。
