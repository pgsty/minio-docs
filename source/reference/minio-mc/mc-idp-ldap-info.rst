.. _minio-mc-idp-ldap-info:

====================
``mc idp ldap info``
====================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc idp ldap info


描述
----

.. start-mc-idp-ldap-info-desc

:mc:`mc idp ldap info` 命令输出指定 MinIO 部署上 AD/LDAP 提供程序的当前配置。

.. end-mc-idp-ldap-info-desc

.. tab-set::

   .. tab-item:: 示例

      以下示例输出 ``myminio`` 部署上的 AD/LDAP 配置设置。

      .. code-block:: shell
         :class: copyable

         mc idp ldap info     \
                     myminio

   .. tab-item:: 语法

      该命令具有以下语法：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] idp ldap info   \
                                   ALIAS

      - 将 ``ALIAS`` 替换为 MinIO 部署的 :ref:`alias <alias>`，以获取 AD/LDAP 集成信息。

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   要输出当前 AD/LDAP 配置的 MinIO 部署 :ref:`alias <alias>`。

   例如：

   .. code-block:: none

      mc idp ldap info myminio



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
