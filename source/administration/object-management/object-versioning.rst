.. _minio-bucket-versioning:

=================
存储桶版本控制
=================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. container:: extlinks-video

   - `Versioning overview <https://youtu.be/XGOiwV6Cbuk?ref=docs>`__
   - `Versioning lab <https://youtu.be/nFUI2N5zH34?ref=docs>`__

概述
----

MinIO 支持在单个存储桶中保留对象的多个“版本”。

启用后，版本控制允许 MinIO 保留同一对象的多个迭代版本。
原本会覆盖现有对象的写入操作，将改为为该对象创建一个新版本。
MinIO 版本控制可以防止意外覆盖和删除，同时支持对写入操作执行“撤销”。
配置 :ref:`对象锁定和保留规则 <minio-object-locking>` 的前提条件之一，就是启用存储桶版本控制。

对于已启用版本控制的存储桶，写入操作会为该对象创建一个带有唯一版本 ID 的新版本。
MinIO 会将对象的“最新”版本标记为客户端默认获取的版本。
之后，客户端可以显式选择列出、获取或移除某个特定对象版本。

可以定义 :ref:`对象过期 <minio-lifecycle-management-create-expiry-rule>` 规则，按版本数量或版本日期等条件移除不再需要的对象版本。

带版本对象的读取操作
~~~~~~~~~~~~~~~~~~~~

查看本组中的四张图片，了解 MinIO 如何在启用版本控制的存储桶中获取对象。
使用图片两侧的箭头可在各张图片之间切换。

.. card-carousel:: 1

   .. card:: 单一版本对象

      .. image:: /images/retention/minio-versioning-single-version.svg
         :alt: 单一版本对象
         :align: center

      MinIO 会在写入操作中为每个对象添加唯一的版本 ID。

   .. card:: 多版本对象

      .. image:: /images/retention/minio-versioning-multiple-versions.svg
         :alt: 多版本对象
         :align: center

      MinIO 会保留对象的所有版本，并将最近的版本标记为“最新”版本。

   .. card:: 获取对象最新版本

      .. image:: /images/retention/minio-versioning-retrieve-latest-version.svg
         :alt: 多版本对象
         :align: center

      未指定版本 ID 的读取请求会返回该对象的最新版本。

   .. card:: 获取特定对象版本

      .. image:: /images/retention/minio-versioning-retrieve-single-version.svg
         :alt: 多版本对象
         :align: center

      在读取操作中指定版本 ID，即可获取对象的特定版本。

.. versionchanged:: MinIO Server RELEASE.2023-05-04T21-44-30Z

   对于显式目录对象（“prefixes”）的创建、变更或删除，MinIO 不会创建版本。
   在该显式目录对象内创建的对象，仍保留正常的版本控制行为。

MinIO 会根据对象路径隐式推断前缀。
显式创建前缀通常只会出现在 Spark 及类似工作负载中，这类工作负载会在 S3 场景下采用传统 POSIX/HDFS 的目录创建行为。

版本控制按命名空间生效
~~~~~~~~~~~~~~~~~~~~~~

MinIO 使用对象的完整命名空间（即存储桶和对象路径）来判断对象的唯一性。
例如，下列所有命名空间都对应“唯一”对象，对其中任一对象的变更，都会在*该命名空间下*创建新的对象版本：

.. code-block:: shell

   databucket/object.blob
   databucket/blobs/object.blob
   blobbucket/object.blob
   blobbucket/blobs/object.blob

尽管各个命名空间中的 ``object.blob`` 可能是相同的二进制数据，
MinIO 仅在特定命名空间内实施版本控制，因此会将上面的每个 ``object.blob`` 视为彼此独立且唯一的对象。

版本控制与存储容量
~~~~~~~~~~~~~~~~~~

MinIO 不执行增量式或差异式版本控制。
对于频繁变更的工作负载，较旧或老化的对象版本可能会占用大量磁盘空间。

例如，假设一个包含日志数据的对象大小为 1GB。应用向该日志追加 100MB 数据后重新上传到 MinIO。
此时 MinIO 会同时保存该对象的 1GB 和 1.1GB 两个版本。
如果应用连续 10 天每天都重复这一过程，那么该存储桶最终会为这一个对象累计保存超过 14GB 的数据。

MinIO 支持配置 :ref:`对象生命周期管理规则
<minio-lifecycle-management>`，以自动过期或过渡老旧对象版本并释放存储容量。
例如，你可以配置一条规则，使对象版本在变为非当前版本（即不再是该对象的“最新”版本）90 天后自动过期。
更多信息请参见 :ref:`MinIO 对象过期 <minio-lifecycle-management-expiration>`。

你也可以使用以下命令手动移除对象版本：

- :mc-cmd:`mc rm --versions` - 删除对象的所有版本。
- :mc-cmd:`mc rm --versions --older-than <mc rm --older-than>` -
   删除对象中早于指定日历日期的所有版本。

.. versionadded:: RELEASE.2024-04-18T19-09-19Z

   如果任一单个对象的版本累计大小超过 1TiB，MinIO 会发出警告。

.. _minio-bucket-versioning-id:

版本 ID 生成
~~~~~~~~~~~~

MinIO 会在写入操作中为每个对象版本生成唯一且不可变的标识符。
每个对象版本 ID 都由一个 128 位固定长度的 :rfc:`UUIDv4 <4122#section-4.4>` 组成。
UUID 的生成具有足够随机性，可在任何环境中以极高概率保证唯一性，计算上也难以猜测，并且无需依赖中心化注册流程或机构来保证唯一性。

.. image:: /images/retention/minio-versioning-multiple-versions.svg
   :alt: 多版本对象
   :width: 600px
   :align: center

MinIO 不支持由客户端管理版本 ID 的分配。
所有版本 ID 的生成都由 MinIO 服务端进程处理。

对于在版本控制禁用或挂起期间创建的对象，MinIO 会使用 ``null`` 版本 ID。
在 S3 操作中将 ``null`` 指定为版本 ID，即可访问或移除这些对象。

.. _minio-bucket-versioning-delete:

版本控制下的删除操作
~~~~~~~~~~~~~~~~~~~~

对带版本的对象执行 ``DELETE`` 操作时，会创建一个 0 字节的 ``DeleteMarker`` 作为该对象的最新版本。
如果对象的最新版本是 ``DeleteMarker``，客户端必须指定版本控制相关标志或标识符，才能对该对象的先前版本执行 ``GET/HEAD/LIST/DELETE`` 操作。
在未显式指定版本的操作中，服务端默认会忽略 ``DeleteMarker`` 对象。

MinIO 可以利用 :ref:`生命周期管理过期规则 <minio-lifecycle-management-expiration>` 自动永久移除对象的旧版本。
否则，可使用手动 ``DELETE`` 操作永久删除非当前版本对象或 ``DeleteMarker`` 对象。

.. admonition:: MinIO 实现幂等 Delete Marker
   :class: note

   .. versionchanged:: RELEASE.2022-08-22T23-53-06Z

   标准 S3 实现处理未指定版本标识符的简单 ``DeleteObject`` 请求时，可能会为同一对象连续创建多个删除标记。
   关于更多细节，请参见 S3 文档中的 :s3-docs:`管理删除标记 <ManagingDelMarkers.html#RemDelMarker>`。

   MinIO 与标准 S3 实现不同，会避免这种潜在的删除标记重复。
   处理未指定版本标识符的 ``Delete`` 请求时，MinIO 最多只会为指定对象创建一个 Delete Marker。
   MinIO **不会** 像 S3 那样创建多个连续的删除标记。

若要永久删除某个对象版本，请执行 ``DELETE`` 操作并指定待删除对象的版本 ID。
这种删除操作**不可逆**。

.. card-carousel:: 1

   .. card:: 删除对象

      .. image:: /images/retention/minio-versioning-delete-object.svg
         :alt: 删除对象
         :align: center

      对带版本的对象执行 ``DELETE`` 操作时，会为该对象生成一个 ``DeleteMarker``。

   .. card:: 读取已删除对象

      .. image:: /images/retention/minio-versioning-retrieve-deleted-object.svg
         :alt: 多版本对象
         :align: center

      客户端默认获取对象的“最新”版本。
      如果最新版本是 ``DeleteMarker``，MinIO 会返回类似 ``404`` 的响应。

   .. card:: 获取已删除对象的先前版本

      .. image:: /images/retention/minio-versioning-retrieve-version-before-delete.svg
         :alt: 获取已删除对象的版本
         :align: center

      即使“最新”版本是 ``DeleteMarker``，客户端仍可通过指定版本 ID 获取该对象的任意先前版本。

   .. card:: 删除特定对象版本

      .. image:: /images/retention/minio-versioning-delete-specific-version.svg
         :alt: 获取已删除对象的版本
         :align: center

      客户端可在 ``DELETE`` 操作中指定版本 ID，以删除某个特定对象版本。
      删除特定版本属于**永久**删除，不会创建 ``DeleteMarker``。

以下 :mc:`mc` 命令可用于处理 ``DeleteMarkers`` 或带版本的对象：

- 使用 :mc-cmd:`mc ls --versions` 查看对象的所有版本，包括删除标记。

- 使用 :mc-cmd:`mc cp --version-id=UUID ... <mc cp --version-id>` 获取 ``UUID`` 匹配的“已删除”对象版本。

- 使用 :mc-cmd:`mc rm --version-id=UUID ... <mc rm --version-id>` 删除 ``UUID`` 匹配的对象版本。

- 使用 :mc-cmd:`mc rm --versions` 删除对象的*所有*版本。

教程
----

启用存储桶版本控制
~~~~~~~~~~~~~~~~~~

你可以使用 MinIO Console、MinIO :mc:`mc` CLI 或兼容 S3 的 SDK 启用版本控制。

使用 :mc:`mc version enable` 命令为现有存储桶启用版本控制：

.. code-block:: shell
   :class: copyable

   mc version enable ALIAS/BUCKET

- 将 ``ALIAS`` 替换为已配置 MinIO 部署的 :mc:`alias <mc alias>`。

- 将 ``BUCKET`` 替换为要启用版本控制的 :mc-cmd:`目标存储桶 <mc version enable ALIAS>`。

在启用版本控制之前创建的对象，其 :ref:`版本 ID <minio-bucket-versioning-id>` 为 ``null``。

从版本控制中排除前缀
~~~~~~~~~~~~~~~~~~~~

你可以使用 :ref:`MinIO Client <minio-client>` 将某些 :ref:`前缀 <minio-admin-concepts-organize-objects>` 排除在版本控制之外。
这对于 Spark/Hadoop 等起初会使用临时前缀创建对象的工作负载尤其有用。

.. admonition:: 复制和对象锁定都要求启用版本控制
   :class: note

   MinIO 依赖版本控制来支持 :term:`复制 <replication>`。
   位于排除前缀中的对象不会复制到任何对等站点或远程站点。

   对于已 :ref:`启用对象锁定 <minio-object-locking>` 的存储桶，MinIO 不支持从版本控制中排除前缀。

- 使用带 :mc-cmd:`~mc version enable --excluded-prefixes` 选项的 :mc:`mc version enable`：

  .. code-block:: shell
     :class: copyable

     mc version enable --excluded-prefixes "prefix1, prefix2" ALIAS/BUCKET

  - 将 ``ALIAS`` 替换为已配置 MinIO 部署的 :mc:`alias <mc alias>`。

  - 将 ``BUCKET`` 替换为要为其排除 :ref:`前缀 <minio-admin-concepts-organize-objects>` 的 :s3-docs:`存储桶 <UsingBucket.html>` 名称。

:mc-cmd:`~mc version enable --excluded-prefixes` 中的前缀列表会匹配前缀或名称中包含指定字符串的所有对象，其行为类似 ``prefix*`` 形式的正则表达式。
若仅按前缀匹配对象，请使用 ``prefix/*``。

例如，以下命令会将前缀或名称中包含 ``_test`` 或 ``_temp`` 的对象排除在版本控制之外：

  .. code-block:: shell
     :class: copyable

     mc version enable --excluded-prefixes "_test, _temp" local/my-bucket

每个存储桶最多可排除 10 个前缀。
若要添加或移除前缀，请使用更新后的列表再次执行 :mc:`mc version enable` 命令。
新列表会替换先前的列表。

若要查看当前排除的前缀，请使用带 ``--json`` 选项的 :mc:`mc version info`：

  .. code-block:: shell
     :class: copyable

     mc version info ALIAS/BUCKET --json

命令输出类似如下，排除前缀列表位于 ``ExcludedPrefixes`` 属性中：

.. code-block:: shell

     $ mc version info local/my-bucket --json
     {
      "Op": "info",
      "status": "success",
      "url": "local/my-bucket",
      "versioning": {
       "status": "Enabled",
       "MFADelete": "",
       "ExcludedPrefixes": [
        "prefix1, prefix2"
       ]
      }
     }

若要禁用前缀排除并恢复对所有前缀启用版本控制，请在不带 :mc-cmd:`~mc version enable --excluded-prefixes` 的情况下再次执行 :mc:`mc version enable`：

  .. code-block:: shell
     :class: copyable

     mc version enable ALIAS/BUCKET

     
从版本控制中排除文件夹
~~~~~~~~~~~~~~~~~~~~~~

你可以使用 :ref:`MinIO Client <minio-client>` 将文件夹排除在版本控制之外。

.. admonition:: 复制和对象锁定都要求启用版本控制
   :class: note

   MinIO 依赖版本控制来支持 :term:`复制 <replication>`。
   位于排除文件夹中的对象不会复制到任何对等站点或远程站点。

   对于已 :ref:`启用对象锁定 <minio-object-locking>` 的存储桶，MinIO 不支持从版本控制中排除文件夹。

.. admonition:: 对象锁定
   :class: note

   已 :ref:`启用对象锁定 <minio-object-locking>` 的存储桶要求启用版本控制，且不支持排除文件夹。

- 使用带 :mc-cmd:`~mc version enable --exclude-folders` 选项的 :mc:`mc version enable`，将名称以 ``/`` 结尾的对象排除在版本控制之外：

  .. code-block:: shell
     :class: copyable

     mc version enable --exclude-folders ALIAS/BUCKET

  - 将 ``ALIAS`` 替换为已配置 MinIO 部署的 :mc:`alias <mc alias>`。

  - 将 ``BUCKET`` 替换为要为其排除 :ref:`文件夹 <minio-admin-concepts-organize-objects>` 的 :s3-docs:`存储桶 <UsingBucket.html>`。

若要检查某个存储桶中的文件夹是否启用版本控制，请使用带 ``--json`` 选项的 :mc:`mc version enable` 命令。
如果 ``ExcludeFolders`` 属性为 ``true``，则该存储桶中的文件夹不启用版本控制。

  .. code-block:: shell
     :class: copyable

     mc version enable --excluded-prefixes ALIAS/BUCKET --json

命令输出类似如下：

.. code-block:: shell

     $ mc version info local/my-bucket --json
     {
      "Op": "info",
      "status": "success",
      "url": "local/my-bucket",
      "versioning": {
       "status": "Enabled",
       "MFADelete": "",
       "ExcludeFolders": true
      }
     }

若要禁用文件夹排除并恢复对所有文件夹启用版本控制，请在不带 :mc-cmd:`~mc version enable --exclude-folders` 的情况下再次执行 :mc:`mc version enable`：

  .. code-block:: shell
     :class: copyable

     mc version enable ALIAS/BUCKET


挂起存储桶版本控制
~~~~~~~~~~~~~~~~~~

你可以随时使用 MinIO :mc:`mc` CLI 或兼容 S3 的 SDK 挂起存储桶版本控制。

使用 :mc:`mc version suspend` 命令挂起现有存储桶的版本控制：

.. code-block:: shell
   :class: copyable

   mc version suspend ALIAS/BUCKET

- 将 ``ALIAS`` 替换为已配置 MinIO 部署的 :mc:`alias <mc alias>`。

- 将 ``BUCKET`` 替换为要禁用版本控制的 :mc-cmd:`目标存储桶 <mc mb ALIAS>`。

在版本控制挂起期间创建的对象会被分配一个 ``null`` :ref:`版本 ID <minio-bucket-versioning-id>`。
在版本控制挂起期间，对对象的任何变更都会覆盖该 ``null`` 版本对象。
MinIO 在挂起版本控制时不会删除或以其他方式更改已有的对象版本。
客户端仍可继续与存储桶中已有的各个对象版本进行交互。
