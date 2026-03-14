.. _minio-mc-ilm-export:

=================
``mc ilm export``
=================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc ilm export

.. versionchanged:: RELEASE.2022-12-24T15-21-38Z

   ``mc ilm export`` 已被 :mc-cmd:`mc ilm rule export` 替代。


语法
----

.. start-mc-ilm-export-desc

:mc:`mc ilm export` 命令用于导出 MinIO 存储桶的对象生命周期管理配置。

.. end-mc-ilm-export-desc

:mc:`mc ilm export` 命令默认输出到 ``STDOUT``。你可以将内容输出到
``.json`` 文件中，以便归档或通过 :mc:`mc ilm import` 导入使用。

.. tab-set::

   .. tab-item:: 示例

      以下命令将 ``myminio`` 部署上 ``mydata`` 存储桶的生命周期管理配置
      导出到 ``mydata-lifecycle-config.json`` 文件：

      .. code-block:: shell
         :class: copyable

         mc ilm export myminio/mydata > mydata-lifecycle-config.json

   .. tab-item:: 语法

      该命令的语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] ilm export ALIAS > STDOUT

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


参数
~~~~

.. mc-cmd:: ALIAS
   
   *必需* MinIO 部署的 :ref:`alias <alias>` 和存储桶完整路径，用于导出该
   存储桶的对象生命周期管理规则。例如：

   .. code-block:: none

      mc ilm export myminio/mydata > bucket-lifecycle.json

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

导出存储桶生命周期管理配置
~~~~~~~~~~~~~~~~~~~~~~~~

.. tab-set::

   .. tab-item:: Example

      以下命令将存储桶生命周期管理配置导出到 ``bucket-lifecycle.json`` 文件：

      .. code-block:: shell
         :class: copyable

         mc ilm export myminio/mybucket > bucket-lifecycle.json

   .. tab-item:: Syntax

      .. code-block:: shell
         :class: copyable

         mc ilm export ALIAS > file.json

      - 将 ``ALIAS`` 替换为 MinIO 部署的 :ref:`alias <alias>` 和需要导出
        对象生命周期管理规则的存储桶：

        ``myminio/mydata``

      - 将 ``file.json`` 替换为用于导出生命周期管理规则的文件名。


行为
----

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
