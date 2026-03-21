.. _minio-mc-admin-svcacct-info:

==============================
``mc admin user svcacct info``
==============================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc admin user svcacct info

.. important::

   此命令已被替代，并将在未来的 MinIO 客户端版本中弃用。

   从 MinIO客户端版本RELEASE.2024-10-08T09-37-26Z 开始，请使用 :mc:`mc admin accesskey info` 命令显示内置 MinIO IDP 用户的 access key 信息。

   对于 AD/LDAP 用户的 access key，请使用 :mc:`mc idp ldap accesskey info` 命令。


语法
----

.. start-mc-admin-svcacct-info-desc

:mc-cmd:`mc admin user svcacct info` 命令返回指定 :ref:`access key <minio-id-access-keys>` 的描述信息。

.. end-mc-admin-svcacct-info-desc

在 MinIO 中，“Access Keys” 与 “Service Accounts” 功能等效，并取代了后者这一概念。

描述输出在可用时包含以下详细信息：

- Access Key
- 指定 access key 的父用户
- access key 状态（``on`` 或 ``off``）
- 策略（单个或多个）
- 注释
- 过期时间

使用 :mc-cmd:`~mc admin user svcacct info --policy` 查看附加的策略。

.. tab-set::

   .. tab-item:: EXAMPLE

      以下命令返回指定 access key 的信息：
  
      .. code-block:: shell  
         :class: copyable 
  
         mc admin user svcacct info myminio myuseraccesskey 

   .. tab-item:: SYNTAX

      该命令语法如下： 
  
      .. code-block:: shell  
         :class: copyable 
  
         mc [GLOBALFLAGS] admin user svcacct info           \  
                                             [--policy]     \  
                                             ALIAS          \  
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

   要显示的 service account access key。

.. mc-cmd:: --policy
   :optional:

   显示附加到指定 service account 的策略。


全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals

示例
----

显示 Service Account 详情
~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc admin user svcacct info` 显示 MinIO 部署上某个 service account 的详细信息：

.. code-block:: shell
   :class: copyable

      mc admin user svcacct info ALIAS ACCESSKEY

- 将 :mc-cmd:`ALIAS <mc admin user add ALIAS>` 替换为 MinIO 部署的 :mc-cmd:`alias <mc alias>`。

- 将 :mc-cmd:`ACCESSKEY <mc admin user svcacct info ACCESSKEY>` 替换为 service account access key。


输出类似如下：

.. code-block:: shell

   AccessKey: myuserserviceaccount
   ParentUser: myuser
   Status: on
   Comment: 
   Policy: implied
   Expiration: no-expiry


显示 Service Account 策略详情
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

使用 :mc-cmd:`mc admin user svcacct info` 显示附加到 service account 的策略：

.. code-block:: shell
   :class: copyable

      mc admin user svcacct info --policy ALIAS ACCESSKEY

- 将 :mc-cmd:`ALIAS <mc admin user add ALIAS>` 替换为 MinIO 部署的 :mc-cmd:`alias <mc alias>`。

- 将 :mc-cmd:`ACCESSKEY <mc admin user svcacct info ACCESSKEY>` 替换为 service account access key。

输出类似如下：

.. code-block:: shell

   {
    "Version": "2012-10-17",
    "Statement": [
     {
      "Effect": "Allow",
      "Action": [
       "s3:*"
      ],
      "Resource": [
       "arn:aws:s3:::*"
      ]
     }
    ]
   }


行为
----

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
