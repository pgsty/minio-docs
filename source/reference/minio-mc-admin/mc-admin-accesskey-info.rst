.. _minio-mc-admin-accesskey-info:

===========================
``mc admin accesskey info``
===========================

.. default-domain:: minio

.. contents:: Table of Contents
   :local:
   :depth: 2

.. mc:: mc admin accesskey info


语法
----

.. start-mc-admin-accesskey-info-desc

:mc-cmd:`mc admin accesskey info` 命令返回指定 :ref:`access key(s) <minio-id-access-keys>` 的描述信息。

.. end-mc-admin-accesskey-info-desc

描述输出会在可用时包含以下详细信息：

- Access Key
- 指定访问密钥的父用户
- 访问密钥状态（``on`` 或 ``off``）
- 策略（单个或多个）
- 注释
- 过期时间

.. tab-set::

   .. tab-item:: 示例

      以下命令返回指定访问密钥的信息：
  
      .. code-block:: shell  
         :class: copyable 
  
         mc admin accesskey info myminio myuseraccesskey 

   .. tab-item:: 语法

      命令语法如下：
  
      .. code-block:: shell  
         :class: copyable 
  
         mc [GLOBALFLAGS] admin accesskey info      \  
                                          ALIAS     \  
                                          ACCESSKEY

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   MinIO 部署的 :mc-cmd:`alias <mc alias>`。

.. mc-cmd:: ACCESSKEY
   :required:

   要显示的访问密钥。

   通过空格分隔多个访问密钥，可返回多个访问密钥的信息。

全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

显示访问密钥详细信息
~~~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc admin accesskey info` 显示 MinIO 部署中某个访问密钥的详细信息：

.. code-block:: shell
   :class: copyable

      mc admin accesskey info myminio myaccesskey

- 将 ``myminio`` 替换为 MinIO 部署的 :mc-cmd:`alias <mc alias>`。

- 将 :mc-cmd:`myaccesskey <mc admin user svcacct info ACCESSKEY>` 替换为要显示信息的访问密钥。
  如需列出多个密钥，请使用空格分隔。

输出类似如下：

.. code-block:: shell

   AccessKey: myuserserviceaccount
   ParentUser: myuser
   Status: on
   Comment: 
   Policy: implied
   Expiration: no-expiry

行为
----

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
