.. start-rewind-desc

指示 |command| 仅对指定时间点存在的对象版本执行操作。

- 如需回溯到过去的特定日期，请将该日期指定为 ISO8601 格式的时间戳。
  例如：``--rewind "2020.03.24T10:00"``。

- 如需按时间长度回溯，请将该时长指定为 ``#d#hh#mm#ss`` 格式的字符串。
  例如：``--rewind "1d2hh3mm4ss"``。

|rewind| 要求指定的 |alias| 指向支持
:ref:`minio-bucket-versioning` 的 S3 兼容服务。对于 MinIO 部署，
请使用 :mc:`mc version` 启用或禁用存储桶版本控制。

.. end-rewind-desc

.. start-versions-desc

指示 |command| 对存储桶中存在的所有对象版本执行操作。

|versions| 要求指定的 |alias| 指向支持
:ref:`minio-bucket-versioning` 的 S3 兼容服务。对于 MinIO 部署，
请使用 :mc:`mc version` 启用或禁用存储桶版本控制。

.. end-versions-desc

.. start-version-id-desc

指示 |command| 仅对指定的对象版本执行操作。

|versionid| 要求指定的 |alias| 指向支持
:ref:`minio-bucket-versioning` 的 S3 兼容服务。对于 MinIO 部署，
请使用 :mc:`mc version` 启用或禁用存储桶版本控制。

.. end-version-id-desc

.. start-versioning-admonition

.. admonition:: 需要版本控制
   :class: note

   要使用此功能，|command| 需要启用
   :ref:`存储桶版本控制 <minio-bucket-versioning>`。
   请使用 :mc:`mc version` 在存储桶上启用版本控制。

.. end-versioning-admonition
