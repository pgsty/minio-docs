.. _minio-mc-ilm-rule-import:

======================
``mc ilm rule import``
======================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc ilm rule import

.. versionchanged:: RELEASE.2022-12-24T15-21-38Z

   ``mc ilm rule import`` 替代 ``mc ilm import``。

语法
----

.. start-mc-ilm-rule-import-desc

:mc:`mc ilm rule import` 命令导入对象生命周期管理配置，
并将其应用到 MinIO 存储桶。

.. end-mc-ilm-rule-import-desc

:mc:`mc ilm rule import` 命令默认从 ``STDIN`` 导入。
你可以从 ``.json`` 文件输入内容，例如由 :mc:`mc ilm rule export` 生成的文件。

.. tab-set::

   .. tab-item:: 示例

      以下命令从 ``mydata-lifecycle-config.json`` 导入生命周期管理配置，
      并将其应用到 ``myminio`` 部署中的 ``mydata`` 存储桶：

      .. code-block:: shell
         :class: copyable

         mc ilm rule import myminio/mydata < mydata-lifecycle-config.json

   .. tab-item:: 语法

      命令语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] ilm rule import ALIAS < STDIN

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~

.. mc-cmd:: ALIAS
   :required:
   
   MinIO 部署上用于导入对象生命周期管理规则的存储桶的 :ref:`alias <alias>` 和完整路径。
   例如：

   .. code-block:: none

      mc ilm rule import myminio/mydata < bucket-lifecycle.json



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

      以下命令从 ``bucket-lifecycle.json`` 文件导入
      存储桶生命周期管理配置：

      .. code-block:: shell
         :class: copyable

         mc ilm rule import myminio/mybucket < bucket-lifecycle.json

   .. tab-item:: 语法

      .. code-block:: shell
         :class: copyable

         mc ilm rule import ALIAS < file.json

      - 将 ``ALIAS`` 替换为 MinIO 部署的 :ref:`alias <alias>` 以及要导入对象生命周期管理规则的存储桶：

        ``myminio/mydata``

      - 将 ``file.json`` 替换为用于导入生命周期管理规则的文件名。


所需权限
--------

有关导入规则所需的权限，请参阅父命令中的 :ref:`required permissions <minio-mc-ilm-rule-permissions>`。


行为
----

导入配置会覆盖现有规则
~~~~~~~~~~~~~~~~~~~~~~

:mc:`mc ilm rule import` 会使用导入的 JSON 配置中定义的规则
替换当前的存储桶生命周期管理规则。

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
