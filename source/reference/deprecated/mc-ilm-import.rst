.. _minio-mc-ilm-import:

=================
``mc ilm import``
=================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc ilm import

.. versionchanged:: RELEASE.2022-12-24T15-21-38Z

   ``mc ilm import`` 已被 :mc-cmd:`mc ilm rule import` 替代。


语法
----

.. start-mc-ilm-import-desc

:mc:`mc ilm import` 命令会导入对象生命周期管理配置，并将其应用到 MinIO
存储桶。

.. end-mc-ilm-import-desc

:mc:`mc ilm import` 命令默认从 ``STDIN`` 导入。你可以从 ``.json`` 文件输入内容，
例如由 :mc:`mc ilm export` 生成的文件。

.. tab-set::

   .. tab-item:: 示例

      以下命令从 ``mydata-lifecycle-config.json`` 导入生命周期管理配置，并将其
      应用到 ``myminio`` 部署上的 ``mydata`` 存储桶：

      .. code-block:: shell
         :class: copyable

         mc ilm import myminio/mydata < mydata-lifecycle-config.json

   .. tab-item:: 语法

      该命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] ilm import ALIAS < STDIN

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~

.. mc-cmd:: ALIAS
   
   *必需* 用于导入对象生命周期管理规则的 MinIO 部署 :ref:`alias <alias>`
   和存储桶完整路径。例如：

   .. code-block:: none

      mc ilm import myminio/mydata < bucket-lifecycle.json



全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

导入存储桶生命周期管理配置
~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tab-set::

   .. tab-item:: 示例

      以下命令从 ``bucket-lifecycle.json`` 文件导入存储桶生命周期管理配置：

      .. code-block:: shell
         :class: copyable

         mc ilm import myminio/mybucket < bucket-lifecycle.json

   .. tab-item:: 语法

      .. code-block:: shell
         :class: copyable

         mc ilm import ALIAS < file.json

      - 将 ``ALIAS`` 替换为 MinIO 部署的 :ref:`alias <alias>` 和要导入对象
        生命周期管理规则的存储桶：

        ``myminio/mydata``

      - 将 ``file.json`` 替换为要从中导入生命周期管理规则的文件名。


行为
----

导入配置会覆盖现有规则
~~~~~~~~~~~~~~~~~~~~~~

:mc:`mc ilm import` 会将当前存储桶生命周期管理规则替换为导入的 JSON 配置中定义
的规则。

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
