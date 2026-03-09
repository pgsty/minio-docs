.. _minio-mc-ilm-rule-export:

======================
``mc ilm rule export``
======================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc ilm rule export

.. versionchanged:: RELEASE.2022-12-24T15-21-38Z

   ``mc ilm rule export`` 替代 ``mc ilm export``。

语法
----

.. start-mc-ilm-rule-export-desc

:mc:`mc ilm rule export` 命令用于导出 MinIO 存储桶的对象生命周期管理配置。

.. end-mc-ilm-rule-export-desc

:mc:`mc ilm rule export` 命令默认输出到 ``STDOUT``。
你可以将内容输出到 ``.json`` 文件中进行归档，或使用 :mc:`mc ilm rule import` 导入。

.. tab-set::

   .. tab-item:: 示例

      以下命令将 ``myminio`` 部署上 ``mydata`` 存储桶的生命周期管理配置
      导出到 ``mydata-lifecycle-config.json`` 文件：

      .. code-block:: shell
         :class: copyable

         mc ilm rule export myminio/mydata > mydata-lifecycle-config.json

   .. tab-item:: 语法

      该命令的语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] ilm rule export ALIAS

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


参数
~~~~

.. mc-cmd:: ALIAS
   :required:
   
   要导出对象生命周期管理规则的 MinIO 部署中存储桶的 :ref:`alias <alias>` 和完整路径。
   例如：

   .. code-block:: none

      mc ilm rule export myminio/mydata > bucket-lifecycle.json

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

导出存储桶生命周期管理配置
~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tab-set::

   .. tab-item:: 示例

      以下命令将存储桶生命周期管理配置导出到 ``bucket-lifecycle.json`` 文件：

      .. code-block:: shell
         :class: copyable

         mc ilm rule export myminio/mybucket > bucket-lifecycle.json

   .. tab-item:: 语法

      .. code-block:: shell
         :class: copyable

         mc ilm rule export ALIAS > file.json

      - 将 ``ALIAS`` 替换为 MinIO 部署的 :ref:`alias <alias>` 以及要导出对象生命周期管理规则的存储桶：

        ``myminio/mydata``

      - 将 ``file.json`` 替换为用于导出生命周期管理规则的文件名。

所需权限
--------

有关导出规则所需的权限，请参阅父命令中的 :ref:`required permissions <minio-mc-ilm-rule-permissions>`。


行为
----

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
