======================
``mc support upload``
======================

.. default-domain:: minio

.. contents:: Table of Contents
   :local:
   :depth: 1

.. mc:: mc support upload


Description
-----------

.. start-mc-support-upload-desc

:mc:`mc support upload` 将文件从本地文件系统复制到 SUBNET 工单。

.. end-mc-support-upload-desc

.. include:: /includes/common-mc-support.rst
   :start-after: start-minio-only
   :end-before: end-minio-only

Syntax
------
      
:mc:`mc support profile` 命令具有以下语法：

.. code-block:: shell

   mc [GLOBALFLAGS] support profile              \
                            ALIAS                \
                            FILE                 \
                            [--comment "string"] \
                            [--enc]              \
                            [--issue integer]

Parameters
~~~~~~~~~~

.. mc-cmd:: ALIAS
   :required:

   MinIO 部署的 :ref:`alias <alias>`。

.. mc-cmd:: FILE
   :required:

   要上传到 SUBNET 的文件路径。

.. mc-cmd:: --comment
   :optional:

   上传文件时，向 issue 附加一条消息。

.. mc-cmd:: --enc 
   :optional:

   对上传内容进行加密。
   用于加密的密钥仅 MinIO 可访问。

.. mc-cmd:: --issue
   :optional:

   指定要添加该文件的 issue 编号。
   如果未指定，则文件会上传到通用 issue 编号 ``0``。

Global Flags
~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

Examples
--------

Upload a file to an issue
~~~~~~~~~~~~~~~~~~~~~~~~~

此命令将本地文件系统中的文件 ``./trace.log`` 上传到别名为 ``minio1`` 的部署对应的 SUBNET issue ``10001``。

.. code-block:: shell
   :class: copyable

   mc support upload --issue 10001 minio1 ./trace.log 


Upload a file to an issue with a comment for MinIO Engineers
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

此命令将本地文件系统中的文件 ``./trace.log`` 上传到别名为 ``minio1`` 的部署对应的 SUBNET issue ``10001``。
该命令还会附加一条关于该文件的注释，供 MinIO Engineers 查看。

.. code-block:: shell
   :class: copyable

   mc support upload --issue 10001 --comment "here is the requested trace log" minio1 ./trace.log
