=========
``mc od``
=========

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc od

语法
----

.. start-mc-od-desc

:mc:`mc od` 命令将本地文件按指定的分片数量与分片大小复制到远程位置。
该命令会输出上传该文件所耗费的时间。

.. end-mc-od-desc

使用 :mc:`mc od` 可模拟 Linux ``dd`` 命令的功能。

.. tab-set::

   .. tab-item:: 示例

      以下命令将文件的 200MiB 上传到存储桶中，分为 5 个 40MiB 的分片。
      输出会显示上传结果，包括完成上传所用的时长。

      .. code-block:: shell
         :class: copyable

         mc od if=file.zip of=myminio/mybucket/file.zip size=40MiB parts=5

      如果传入 ``--json`` :ref:`全局参数 <minio-mc-global-options>`，命令输出类似如下：

      .. code-block:: json

         {
           "source": "home/user/file.zip"
           "target": "myminio/mybucket/file.zip"
           "partSize": 41943040
           "totalSize": 209715200
           "parts": 5
           "elapsed": "314ms"
         }

   .. tab-item:: 语法

      命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] od                                            \
                          if=<path of source file to upload>            \
                          of=<target MinIO path to upload to>           \
                          [size=<size of file>]                         \
                          [parts=<number of parts to split file into>]  \
                          [skip=<number of parts to skip>]

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


参数
~~~~

.. mc-cmd:: if
   :required:

   用于上传的源对象路径。
   使用相对于当前位置的完整路径。

   .. code-block:: none

      mc od if=file.zip of=myminio/mybucket/file.zip

.. mc-cmd:: of
   :required:

   上传对象的完整目标路径。

.. mc-cmd:: size
   :optional:

   文件上传时每个分片的大小。
   如未指定，MinIO 会根据源流确定分片大小。

.. mc-cmd:: parts
   :optional:

   上传时将对象拆分成的分片数量。
   如未指定，MinIO 会根据源流大小确定分片数量。

.. mc-cmd:: skip
   :optional:

   上传过程中要跳过的文件分片数量。
   例如，可使用该选项仅上传对象的一部分分片，以测试一个大文件（分片较多）的上传速度。

全局参数
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

以 40MiB 分片上传完整文件
~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc:`mc od` 可按指定大小的一组分片将文件上传到 MinIO。
:mc-cmd:`~mc od size` 选项用于指定期望的分片大小。

.. code-block:: shell
   :class: copyable

   mc od if=file.zip of=myminio/mybucket/file.zip size=40MiB

- 将 ``myminio/mybucket/file.zip`` 替换为要上传的对象或文件流路径。

- 将 :mc-cmd:`size <mc od size>` 替换为期望的对象分片大小。

MinIO 会检查源文件并将其拆分为所需数量的分片，确保没有任何分片超过指定的 40MiB 分片大小。

上传文件的前五个 40 MiB 分片
~~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc:`mc od` 可按指定分片大小，将文件的部分分片上传到 MinIO。
:mc-cmd:`~mc od size` 选项用于指定期望的分片大小。
:mc-cmd:`~mc od parts` 选项用于指定该对象要使用的总分片数。

.. code-block:: shell
   :class: copyable

   mc od if=file.zip of=myminio/mybucket/file.zip size=40MiB parts=5

- 将 ``myminio/mybucket/file.zip`` 替换为要上传的对象或文件流路径。
- 将 :mc-cmd:`size <mc od size>` 替换为期望的对象分片大小。
- 将 :mc-cmd:`parts <mc od parts>` 替换为该对象期望使用的分片数。

在该命令示例中，如果源对象流大于 200MiB（40MiB × 5 个分片），则仅上传文件的前 200MiB。

.. important:: 

   以这种方式使用该命令可能无法上传对象的全部内容。

将完整文件分 5 个分片上传
~~~~~~~~~~~~~~~~~~~~~~~~

将源文件拆分为指定数量的分片，然后把文件的所有分片上传到 MinIO 目标位置。

.. code-block:: shell
   :class: copyable

   mc od if=file.zip of=myminio/mybucket/file.zip parts=5

上述命令会将源文件均分为五个分片，然后上传这些分片。

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
