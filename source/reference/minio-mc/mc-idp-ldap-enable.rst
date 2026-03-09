.. _minio-mc-idp-ldap-enable:

======================
``mc idp ldap enable``
======================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc idp ldap enable


描述
----

.. start-mc-idp-ldap-enable-desc

:mc:`mc idp ldap enable` 命令用于启用当前已配置的 AD/LDAP 提供程序。

.. end-mc-idp-ldap-enable-desc

.. tab-set::

   .. tab-item:: 示例

      以下示例在 ``myminio`` 部署上启用 AD/LDAP 配置。

      .. code-block:: shell
         :class: copyable

         mc idp ldap enable   \
                     myminio

   .. tab-item:: 语法

      该命令具有以下语法：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] idp ldap enable  \
                                   ALIAS

      - 将 ``ALIAS`` 替换为 MinIO 部署的 :ref:`alias <alias>`，以启用 AD/LDAP 集成。

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   要启用 AD/LDAP 集成的 MinIO 部署的 :ref:`alias <alias>`。

   例如：

   .. code-block:: none

      mc idp ldap enable myminio



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
