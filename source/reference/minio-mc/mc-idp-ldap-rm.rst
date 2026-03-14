.. _minio-mc-idp-ldap-rm:

==================
``mc idp ldap rm``
==================

.. default-domain:: minio

.. contents:: 目录
   :name: table-of-contents
   :local:
   :depth: 2

.. mc:: mc idp ldap rm
.. mc:: mc idp ldap remove


说明
-----------

.. start-mc-idp-ldap-rm-desc

:mc:`mc idp ldap rm` 命令移除 AD/LDAP 提供方的现有配置。

.. end-mc-idp-ldap-rm-desc

:mc:`mc idp ldap rm` 也称为 :mc:`mc idp ldap remove`。

.. tab-set::

   .. tab-item:: 示例

      以下示例移除 ``myminio`` 部署的 AD/LDAP 提供方设置。

      .. code-block:: shell
         :class: copyable

         mc idp ldap rm       \
                     myminio

   .. tab-item:: 语法

      该命令的语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] idp ldap rm     \
                                   ALIAS

      - 将 ``ALIAS`` 替换为 MinIO 部署的 :ref:`alias <alias>`，以移除 AD/LDAP 集成。

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~~~~~~~

.. mc-cmd:: ALIAS
   :required:

   要移除其当前 AD/LDAP 配置的 MinIO 部署的 :ref:`alias <alias>`。

   例如：

   .. code-block:: none

      mc idp ldap rm myminio



全局标志
~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-globals
   :end-before: end-minio-mc-globals


行为
--------

S3 兼容性
~~~~~~~~~~~~~~~~

.. include:: /includes/common-minio-mc.rst
   :start-after: start-minio-mc-s3-compatibility
   :end-before: end-minio-mc-s3-compatibility
