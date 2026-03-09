

.. Root API Access

.. start-minio-data-compression-default-excluded-desc

.. list-table::
   :header-rows: 1
   :widths: 30 30 40
   :width: 100%

   * - 对象类型
     - 文件扩展名
     - 媒体（MIME）类型

   * - 音频
     -
     - ``audio/*``

   * - 视频
     - | ``*.mp4``
       | ``*.mkv``
       | ``*.mov``
     - ``video/*``

   * - 图像
     - | ``*.jpg``
       | ``*.png``
       | ``*.gif``
     - ``application/x-compress`` (LZW)

   * - 7ZIP 压缩文件
     - ``*.7z``
     -

   * - BZIP2 压缩文件
     - ``*.bz2``
     - ``application/x-bz2``

   * - GZIP 压缩文件
     - ``*.gz``
     - ``application/x-gzip``

   * - RAR 压缩文件
     - ``*.rar``
     -

   * - LZMA 压缩文件
     - ``*.xz``
     - ``application/x-xz``

   * - ZIP 压缩文件
     - ``*.zip``
     - | ``application/zip``
       | ``application-x-zip-compressed``

   * - 小于 4 KiB
     -
     -

.. end-minio-data-compression-default-excluded-desc

.. start-minio-data-compression-default-desc

+-----------------+--------------------------+
| 文件扩展名      | 媒体（MIME）类型         |
+=================+==========================+
| ``.txt``        | ``text/*``               |
|                 |                          |
| ``.log``        | ``application/json``     |
|                 |                          |
| ``.csv``        | ``application/xml``      |
|                 |                          |
| ``.json``       | ``binary/octet-stream``  |
|                 |                          |
| ``.tar``        |                          |
|                 |                          |
| ``.xml``        |                          |
|                 |                          |
| ``.bin``        |                          |
+-----------------+--------------------------+

.. end-minio-data-compression-default-desc

.. start-minio-api-sync-events

启用同步 :ref:`存储桶通知 <minio-bucket-notifications>`。

指定 ``on`` 可让 MinIO 在远端目标收到事件并返回成功之后，再继续处理后续事件。

默认值为 ``off``，即异步存储桶通知。在这种模式下，MinIO 不会等待远端目标在收到
事件后返回成功。

.. end-minio-api-sync-events

.. start-minio-settings-no-config-option

此设置没有对应的配置项。

.. end-minio-settings-no-config-option

.. start-minio-settings-defined

你可以通过以下方式建立或修改设置：

- 在启动或重启 MinIO Server 之前，在宿主机系统上定义 *环境变量*。
  如何定义环境变量，请参考所用操作系统的文档。
- 使用 :mc:`mc admin config set` 定义 *配置项*。

如果同时定义了环境变量和对应的配置项，MinIO 使用环境变量的值。

有些设置只有环境变量或配置项中的一种，而不是两者同时存在。

.. end-minio-settings-defined

.. start-minio-settings-test-before-prod

.. important::

   每个配置项都会控制 MinIO 的基础行为和功能。
   MinIO **强烈建议** 先在 DEV 或 QA 等较低级别环境中测试配置变更，再应用到生产环境。

.. end-minio-settings-test-before-prod
