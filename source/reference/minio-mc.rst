.. _minio-client:

===========
MinIO 客户端
===========

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 1

.. container:: extlinks-video

   - `MinIO 客户端 (MC) 命令简介 <https://www.youtube.com/watch?v=pukQgDdXfqA>`__
   - `在 Linux 上安装和运行 MinIO <https://www.youtube.com/watch?v=74usXkZpNt8&list=PLFOIsHSSYIK1BnzVY66pCL-iJ30Ht9t1o>`__

.. mc:: mc

MinIO Client :mc-cmd:`mc` 命令行工具为 ``ls``、``cat``、``cp``、``mirror`` 和 ``diff``
等 UNIX 命令提供了现代替代方案，同时支持文件系统和兼容 Amazon S3 的云存储服务。

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: minio-mc-s3-compatibility

:mc-cmd:`mc` 的语法如下：

.. code-block:: shell

   mc [GLOBALFLAGS] COMMAND --help

支持的命令列表参见 :ref:`minio-mc-commands`。

.. _mc-client-versioning:

与 MinIO 服务端的版本对齐
--------------------------

MinIO 客户端与 MinIO 服务端独立发布。

为获得最佳功能与兼容性，建议使用与 MinIO 服务端版本发布时间接近的 MinIO 客户端版本。
例如，使用与 MinIO 服务端同一天发布或晚于 MinIO 服务端版本发布的 MinIO 客户端。

可以安装比 MinIO 服务端 更新的 MinIO 客户端版本。
但如果 MinIO 客户端与 MinIO 服务端的版本偏差过大，可能因差异导致更多告警或错误。
例如，虽然复制相关的核心 S3 API（:mc:`mc cp`）可能保持不变，但某些功能或 flag 仅在客户端与服务端版本对齐时才可用或稳定。

.. _mc-install:

快速开始
--------

1) 安装 ``mc``
~~~~~~~~~~~~~~

在主机上安装 :program:`mc` 命令行工具。点击与主机操作系统或环境对应的标签页：

.. include:: /includes/minio-mc-installation.rst

2) 为兼容 S3 的服务创建别名
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. important::

   以下示例会临时禁用 bash history，以降低身份认证凭据明文泄露的风险。
   这是一项基础安全措施，无法覆盖所有可能的攻击向量。
   对于在命令行输入敏感信息，请遵循你所用操作系统的安全最佳实践。

使用 :mc:`mc alias set` 命令将 Amazon S3 兼容服务添加到
:mc-cmd:`mc` :ref:`配置 <mc-configuration>` 中。

.. code-block:: shell
   :class: copyable

   bash +o history
   mc alias set ALIAS HOSTNAME ACCESS_KEY SECRET_KEY
   bash -o history

- 将 ``ALIAS`` 替换为与 S3 服务关联的名称。
  :mc-cmd:`mc` 命令通常要求提供 ``ALIAS`` 作为参数，
  以标识要执行操作的 S3 服务。

- 将 ``HOSTNAME`` 替换为 S3 服务的 URL endpoint 或 IP 地址。

- 将 ``ACCESS_KEY`` 和 ``SECRET_KEY`` 替换为该 S3 服务上某个用户的
  access key 和 secret key。

将各参数替换为所需值。
如果省略 ``ACCESS_KEY`` 和 ``SECRET_KEY``，命令会在 CLI 中提示输入这两个值。

以下每个标签页都包含一个特定提供商示例：

.. tab-set::

   .. tab-item:: MinIO 服务端

      .. code-block:: shell
         :class: copyable

         mc alias set myminio https://minioserver.example.net ACCESS_KEY SECRET_KEY

   .. tab-item:: AWS S3 Storage

      .. code-block:: shell
         :class: copyable

         mc alias set myS3 https://s3.{your-region-code}.amazonaws.com/endpoint ACCESS_KEY SECRET_KEY

   .. tab-item:: Google Cloud Storage

      .. code-block:: shell
         :class: copyable

         mc alias set myGCS https://storage.googleapis.com/endpoint ACCESS_KEY SECRET_KEY

3) 测试连接
~~~~~~~~~~~

使用 :mc-cmd:`mc admin info` 命令测试与
新添加 MinIO 部署的连接：

.. code-block:: shell
   :class: copyable

   mc admin info myminio

如果命令执行成功，会返回该 S3 服务的信息。
如果失败，请检查以下各项：

- 主机可连通 S3 服务 URL（例如使用 ``ping``
  或 ``traceroute``）。

- 指定的 ``ACCESSKEY`` 和 ``SECRETKEY`` 对应 S3 服务上的有效用户。
  该用户必须有权限在该服务上执行操作。

  对于 MinIO 部署，参见 :ref:`minio-access-management`
  获取更多用户访问权限信息。对于其他兼容 S3 的
  服务，请参考该服务文档。

.. _minio-mc-commands:

命令速查
--------

下表列出了 :mc-cmd:`mc` 命令：

.. note::

   MinIO 客户端还包含用于管理 MinIO 部署的管理扩展。
   更完整文档参见 :mc:`mc admin`。

   下表不包含这些命令。

.. list-table::
   :header-rows: 1
   :widths: 30 70
   :width: 100%

   * - 命令
     - 说明

   * - | :mc:`mc alias list`
       | :mc:`mc alias remove`
       | :mc:`mc alias set`
       | :mc:`mc alias import`
       | :mc:`mc alias export`
     - .. include:: /reference/minio-mc/mc-alias.rst
          :start-after: start-mc-alias-desc
          :end-before: end-mc-alias-desc

   * - | :mc:`mc anonymous get`
       | :mc:`mc anonymous get-json`
       | :mc:`mc anonymous links`
       | :mc:`mc anonymous list`
       | :mc:`mc anonymous set`
       | :mc:`mc anonymous set-json`
     - .. include:: /reference/minio-mc/mc-anonymous.rst
          :start-after: start-mc-anonymous-desc
          :end-before: end-mc-anonymous-desc

   * - | :mc:`mc batch describe`
       | :mc:`mc batch generate`
       | :mc:`mc batch list`
       | :mc:`mc batch start`
       | :mc:`mc batch status`
     - .. include:: /reference/minio-mc/mc-batch.rst
          :start-after: start-mc-batch-desc
          :end-before: end-mc-batch-desc

   * - :mc:`mc cat`
     - .. include:: /reference/minio-mc/mc-cat.rst
          :start-after: start-mc-cat-desc
          :end-before: end-mc-cat-desc

   * - :mc:`mc cp`
     - .. include:: /reference/minio-mc/mc-cp.rst
          :start-after: start-mc-cp-desc
          :end-before: end-mc-cp-desc

   * - :mc:`mc diff`
     - .. include:: /reference/minio-mc/mc-diff.rst
          :start-after: start-mc-diff-desc
          :end-before: end-mc-diff-desc

   * - :mc:`mc du`
     - .. include:: /reference/minio-mc/mc-du.rst
          :start-after: start-mc-du-desc
          :end-before: end-mc-du-desc

   * - | :mc:`mc encrypt clear`
       | :mc:`mc encrypt info`
       | :mc:`mc encrypt set`
     - .. include:: /reference/minio-mc/mc-encrypt.rst
          :start-after: start-mc-encrypt-desc
          :end-before: end-mc-encrypt-desc

   * - | :mc:`mc event add`
       | :mc:`mc event ls`
       | :mc:`mc event rm`
     - .. include:: /reference/minio-mc/mc-event.rst
          :start-after: start-mc-event-desc
          :end-before: end-mc-event-desc

   * - :mc:`mc find`
     - .. include:: /reference/minio-mc/mc-find.rst
          :start-after: start-mc-find-desc
          :end-before: end-mc-find-desc

   * - :mc:`mc get`
     - .. include:: /reference/minio-mc/mc-get.rst
          :start-after: start-mc-get-desc
          :end-before: end-mc-get-desc

   * - :mc:`mc head`
     - .. include:: /reference/minio-mc/mc-head.rst
          :start-after: start-mc-head-desc
          :end-before: end-mc-head-desc

   * - | :mc:`mc idp ldap accesskey`
       | :mc:`mc idp ldap accesskey create-with-login`
       | :mc:`mc idp ldap add`
       | :mc:`mc idp ldap disable`
       | :mc:`mc idp ldap enable`
       | :mc:`mc idp ldap info`
       | :mc:`mc idp ldap ls`
       | :mc:`mc idp ldap policy`
       | :mc:`mc idp ldap rm`
       | :mc:`mc idp ldap update`
     - .. include:: /reference/minio-mc/mc-idp-ldap.rst
          :start-after: start-mc-idp-ldap-desc
          :end-before: end-mc-idp-ldap-desc

   * - | :mc:`mc idp openid add`
       | :mc:`mc idp openid disable`
       | :mc:`mc idp openid enable`
       | :mc:`mc idp openid info`
       | :mc:`mc idp openid ls`
       | :mc:`mc idp openid rm`
       | :mc:`mc idp openid update`
     - .. include:: /reference/minio-mc/mc-idp-openid.rst
          :start-after: start-mc-idp-openid-desc
          :end-before: end-mc-idp-openid-desc

   * - | :mc:`mc idp ldap policy attach`
       | :mc:`mc idp ldap policy detach`
       | :mc:`mc idp ldap policy entities`
     - .. include:: /reference/minio-mc/mc-idp-ldap-policy.rst
          :start-after: start-mc-idp-ldap-policy-desc
          :end-before: end-mc-idp-ldap-policy-desc

   * - | :mc:`mc ilm restore`
       | :mc:`mc ilm rule add`
       | :mc:`mc ilm rule edit`
       | :mc:`mc ilm rule export`
       | :mc:`mc ilm rule import`
       | :mc:`mc ilm rule ls`
       | :mc:`mc ilm rule rm`
       | :mc:`mc ilm tier add`
       | :mc:`mc ilm tier check`
       | :mc:`mc ilm tier info`
       | :mc:`mc ilm tier ls`
       | :mc:`mc ilm tier rm`
       | :mc:`mc ilm tier update`
     - .. include:: /reference/minio-mc/mc-ilm.rst
          :start-after: start-mc-ilm-desc
          :end-before: end-mc-ilm-desc

   * - | :mc:`mc legalhold clear`
       | :mc:`mc legalhold info`
       | :mc:`mc legalhold set`
     - .. include:: /reference/minio-mc/mc-legalhold.rst
          :start-after: start-mc-legalhold-desc
          :end-before: end-mc-legalhold-desc

   * - | :mc:`mc license info`
       | :mc:`mc license register`
       | :mc:`mc license update`
     - .. include:: /reference/minio-mc/mc-license.rst
          :start-after: start-mc-license-desc
          :end-before: end-mc-license-desc

   * - :mc:`mc ls`
     - .. include:: /reference/minio-mc/mc-ls.rst
          :start-after: start-mc-ls-desc
          :end-before: end-mc-ls-desc

   * - :mc:`mc mb`
     - .. include:: /reference/minio-mc/mc-mb.rst
          :start-after: start-mc-mb-desc
          :end-before: end-mc-mb-desc

   * - :mc:`mc mirror`
     - .. include:: /reference/minio-mc/mc-mirror.rst
          :start-after: start-mc-mirror-desc
          :end-before: end-mc-mirror-desc

   * - :mc:`mc mv`
     - .. include:: /reference/minio-mc/mc-mv.rst
          :start-after: start-mc-mv-desc
          :end-before: end-mc-mv-desc

   * - :mc:`mc od`
     - .. include:: /reference/minio-mc/mc-od.rst
          :start-after: start-mc-od-desc
          :end-before: end-mc-od-desc

   * - :mc:`mc ping`
     - .. include:: /reference/minio-mc/mc-ping.rst
          :start-after: start-mc-ping-desc
          :end-before: end-mc-ping-desc

   * - :mc:`mc pipe`
     - .. include:: /reference/minio-mc/mc-pipe.rst
          :start-after: start-mc-pipe-desc
          :end-before: end-mc-pipe-desc

   * - :mc:`mc put`
     - .. include:: /reference/minio-mc/mc-put.rst
          :start-after: start-mc-put-desc
          :end-before: end-mc-put-desc

   * - :mc:`mc rb`
     - .. include:: /reference/minio-mc/mc-rb.rst
          :start-after: start-mc-rb-desc
          :end-before: end-mc-rb-desc

   * - :mc:`mc ready`
     - .. include:: /reference/minio-mc/mc-ready.rst
          :start-after: start-mc-ready-desc
          :end-before: end-mc-ready-desc

   * - | :mc:`mc replicate add`
       | :mc:`mc replicate backlog`
       | :mc:`mc replicate export`
       | :mc:`mc replicate import`
       | :mc:`mc replicate ls`
       | :mc:`mc replicate resync`
       | :mc:`mc replicate rm`
       | :mc:`mc replicate status`
       | :mc:`mc replicate update`
     - .. include:: /reference/minio-mc/mc-replicate.rst
          :start-after: start-mc-replicate-desc
          :end-before: end-mc-replicate-desc

   * - | :mc:`mc retention clear`
       | :mc:`mc retention info`
       | :mc:`mc retention set`
     - .. include:: /reference/minio-mc/mc-retention.rst
          :start-after: start-mc-retention-desc
          :end-before: end-mc-retention-desc

   * - :mc:`mc rm`
     - .. include:: /reference/minio-mc/mc-rm.rst
          :start-after: start-mc-rm-desc
          :end-before: end-mc-rm-desc

   * - | :mc:`mc share download`
       | :mc:`mc share ls`
       | :mc:`mc share upload`
     - .. include:: /reference/minio-mc/mc-share.rst
          :start-after: start-mc-share-desc
          :end-before: end-mc-share-desc

   * - :mc:`mc sql`
     - .. include:: /reference/minio-mc/mc-sql.rst
          :start-after: start-mc-sql-desc
          :end-before: end-mc-sql-desc

   * - :mc:`mc stat`
     - .. include:: /reference/minio-mc/mc-stat.rst
          :start-after: start-mc-stat-desc
          :end-before: end-mc-stat-desc

   * - | :mc:`mc support callhome`
       | :mc:`mc support diag`
       | :mc:`mc support inspect`
       | :mc:`mc support perf`
       | :mc:`mc support profile`
       | :mc:`mc support proxy`
       | :mc:`mc support top api`
       | :mc:`mc support top disk`
       | :mc:`mc support top locks`
       | :mc:`mc support upload`
     - .. include:: /reference/minio-mc/mc-support.rst
          :start-after: start-mc-support-desc
          :end-before: end-mc-support-desc

   * - | :mc:`mc tag list`
       | :mc:`mc tag remove`
       | :mc:`mc tag set`
     - .. include:: /reference/minio-mc/mc-tag.rst
          :start-after: start-mc-tag-desc
          :end-before: end-mc-tag-desc

   * - :mc:`mc tree`
     - .. include:: /reference/minio-mc/mc-tree.rst
          :start-after: start-mc-tree-desc
          :end-before: end-mc-tree-desc

   * - :mc:`mc undo`
     - .. include:: /reference/minio-mc/mc-undo.rst
          :start-after: start-mc-undo-desc
          :end-before: end-mc-undo-desc

   * - :mc:`mc update`
     - .. include:: /reference/minio-mc/mc-update.rst
          :start-after: start-mc-update-desc
          :end-before: end-mc-update-desc

   * - | :mc:`mc version enable`
       | :mc:`mc version info`
       | :mc:`mc version suspend`
     - .. include:: /reference/minio-mc/mc-version.rst
          :start-after: start-mc-version-desc
          :end-before: end-mc-version-desc

   * - :mc:`mc watch`
     - .. include:: /reference/minio-mc/mc-watch.rst
          :start-after: start-mc-watch-desc
          :end-before: end-mc-watch-desc

.. _mc-configuration:

配置文件
--------

:mc-cmd:`mc` 使用 ``JSON`` 格式的配置文件来存储
某些类型的信息，例如每个已配置兼容 S3 服务的
:mc-cmd:`aliases <mc alias>`。

在 Linux 和 macOS 上，默认配置文件位置为
``~/.mc/config.json``。

在 Windows 上，:mc-cmd:`mc` 会尝试通过特定环境变量
构造默认文件路径。如果某个变量未设置，:mc-cmd:`mc` 会继续
尝试下一个变量。如果所有尝试都失败，:mc-cmd:`mc` 会返回错误。
下列列表按 :mc-cmd:`mc` 检查顺序说明了可能的文件路径位置：

#. ``HOME\.mc\config.json``
#. ``USERPROFILE\.mc\config.json``
#. ``HOMEDRIVE+HOMEPATH\.mc\config.json``

可使用 ``--config-dir``

.. _minio-mc-certificates:

证书
----

MinIO 客户端将部署使用的证书和 CA 存储在以下路径：

Linux、macOS 和其他类 Unix 系统：

.. code-block:: shell

   ~/.mc/certs/ # certificates
   ~/.mc/certs/CAs/ # Certificate Authorities

Windows 系统：

.. code-block:: shell

   C:\Users\[username]\mc\certs\ # certificates
   C:\Users\[username]\mc\certs\CAs\ # Certificate Authorities

创建新的 :ref:`alias <minio-mc-alias>` 时，MinIO Client 会拉取对端证书、计算公钥指纹，并询问用户是否接受该部署的证书。
如果你决定信任该证书，MinIO Client 会将其添加到上述证书颁发机构路径。

.. note::

   在测试环境中，你可以通过传入 ``--insecure`` flag，跳过部分 MinIO Client 命令的证书检查。

.. _minio-wildcard-matching:

模式匹配
--------

某些命令和 flag 支持模式匹配。
启用后，模式可包含以下任一通配符用于字符替换：

- ``*`` 表示要匹配的一串字符，可位于中间或末尾。
- ``?`` 表示单个字符。

例如，可参考以下通配符使用示例及其结果。

.. list-table::
   :header-rows: 1
   :widths: 40 30 30
   :width: 100%

   * - 模式
     - 文本
     - 匹配结果

   * - ``abc*``
     - ab
     - 匹配

   * - ``abc*``
     - abd
     - 不匹配

   * - ``abc*c``
     - abcd
     - 匹配

   * - ``ab*??d``
     - abxxc
     - 匹配

   * - ``ab*??d``
     - abxc
     - 匹配

   * - ``ab??d``
     - abxc
     - 匹配

   * - ``ab??d``
     - abc
     - 匹配

   * - ``ab??d``
     - abcxdd
     - 不匹配

.. _minio-mc-global-options:

全局选项
--------

.. program:: mc

所有 :ref:`commands <minio-mc-commands>` 都支持以下全局选项。
你也可以使用 :ref:`Environment Variables <minio-server-envvar-mc>` 来定义其中部分选项。

.. option:: --config-dir

   指向 ``JSON`` 格式配置文件的路径，
   :program:`mc` 使用该文件存储数据。有关
   :program:`mc` 如何使用配置文件的更多信息，请参见 :ref:`mc-configuration`。

   或者，设置环境变量 :envvar:`MC_CONFIG_DIR`。

.. option:: --debug

   启用控制台详细输出。

   例如，以下操作会为
   :mc:`mc ls` 命令增加详细输出：

   .. code-block:: shell
      :class: copyable

      mc --debug ls play

   或者，设置环境变量 :envvar:`MC_DEBUG`。

.. option:: --disable-pager, --dp

   .. versionadded:: mc RELEASE.2024-04-29T09-56-05Z

   在 CLI 中禁用 MinIO Client 的分页功能。
   使用后，输出会直接打印到原始 ``STDOUT``。

.. option:: --insecure

   禁用 TLS/SSL 证书校验。允许与证书无效的
   服务器建立 TLS 连接。对不受信任的 S3 主机使用该
   选项时请谨慎。

   或者，设置环境变量 :envvar:`MC_INSECURE`。

.. option:: --json

   启用 `JSON lines <http://jsonlines.org/>`_ 格式输出到
   控制台。

   例如，以下操作会为
   :mc:`mc ls` 命令增加 JSON Lines 输出：

   .. code-block:: shell
      :class: copyable

      mc --json ls play

   或者，设置环境变量 :envvar:`MC_JSON`。

.. option:: --no-color

   禁用控制台输出的内置配色主题。适用于 dumb
   终端。

   或者，设置环境变量 :envvar:`MC_NO_COLOR`。

.. option:: --quiet

   抑制控制台输出。

   或者，设置环境变量 :envvar:`MC_QUIET`。

.. option:: --resolve

   .. versionadded:: mc RELEASE.2024-08-13T05-33-17Z

   创建自定义 DNS 映射，将 HOST 解析到指定 IP 地址。

   使用以下语法：

   .. code-block:: text

      --resolve HOST[:PORT]=IP

   例如：

   .. code-block:: shell
      :class: copyable

      mc alias set --resolve myminio.example.com:9000=192.168.188.118 'myminio' 'https://myminio.example.com:9000' 'miniouser' 'miniosecret'

   可重复该 flag 多次以添加更多自定义 DNS 映射。

.. option:: --version

   显示 :mc-cmd:`mc` 的当前版本。

.. mc-cmd:: --help
   :optional:

   在终端显示命令用法摘要。

.. toctree::
   :titlesonly:
   :hidden:

   /reference/minio-mc/minio-client-settings
   /reference/minio-mc/mc-alias
   /reference/minio-mc/mc-anonymous
   /reference/minio-mc/mc-batch
   /reference/minio-mc/mc-cat
   /reference/minio-mc/mc-cp
   /reference/minio-mc/mc-diff
   /reference/minio-mc/mc-du
   /reference/minio-mc/mc-encrypt
   /reference/minio-mc/mc-event
   /reference/minio-mc/mc-find
   /reference/minio-mc/mc-get
   /reference/minio-mc/mc-head
   /reference/minio-mc/mc-idp-ldap
   /reference/minio-mc/mc-idp-ldap-accesskey
   /reference/minio-mc/mc-idp-ldap-accesskey-create-with-login
   /reference/minio-mc/mc-idp-ldap-policy
   /reference/minio-mc/mc-idp-openid
   /reference/minio-mc/mc-ilm
   /reference/minio-mc/mc-legalhold
   /reference/minio-mc/mc-license
   /reference/minio-mc/mc-ls
   /reference/minio-mc/mc-mb
   /reference/minio-mc/mc-mirror
   /reference/minio-mc/mc-mv
   /reference/minio-mc/mc-od
   /reference/minio-mc/mc-ping
   /reference/minio-mc/mc-pipe
   /reference/minio-mc/mc-put
   /reference/minio-mc/mc-rb
   /reference/minio-mc/mc-ready
   /reference/minio-mc/mc-replicate
   /reference/minio-mc/mc-retention
   /reference/minio-mc/mc-rm
   /reference/minio-mc/mc-share
   /reference/minio-mc/mc-sql
   /reference/minio-mc/mc-stat
   /reference/minio-mc/mc-support
   /reference/minio-mc/mc-tag
   /reference/minio-mc/mc-tree
   /reference/minio-mc/mc-undo
   /reference/minio-mc/mc-update
   /reference/minio-mc/mc-version
   /reference/minio-mc/mc-watch
