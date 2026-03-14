.. _minio-mc-admin-sts-info:

==============================
``mc admin user sts info``
==============================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc admin user sts info


语法
----

.. start-mc-admin-sts-info-desc

:mc-cmd:`mc admin user sts info` 命令用于检索指定 STS 凭证的信息，例如生成该凭证的父 :ref:`MinIO user <minio-internal-idp>`、关联策略和过期时间。

.. end-mc-admin-sts-info-desc

:abbr:`STS (Security Token Service)` 凭证为 MinIO 部署提供临时访问权限。

.. tab-set::

   .. tab-item:: 示例

      以下命令检索具有指定访问密钥的 STS 凭证信息：

      .. code-block:: shell  
         :class: copyable 
  
         mc admin user sts info myminio/ "J123C4ZXEQN8RK6ND35I"

   .. tab-item:: 语法

      该命令的语法如下：
  
      .. code-block:: shell  
         :class: copyable 

         mc [GLOBALFLAGS] admin user sts info          \
                                         [--policy]    \
                                         ALIAS         \
                                         STSACCESSKEY

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   MinIO 部署的 :mc-cmd:`alias <mc alias>`。

.. mc-cmd:: STSACCESSKEY
   :required:

   STS 凭证的访问密钥。

.. mc-cmd:: --policy
   :optional:

   以 JSON 格式打印附加到指定 STS 凭证的策略。


全局标志
~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals


行为
----

S3 兼容性
~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
