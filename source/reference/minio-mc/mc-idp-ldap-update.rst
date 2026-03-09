.. _minio-mc-idp-ldap-update:

======================
``mc idp ldap update``
======================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2

.. mc:: mc idp ldap update


描述
----

.. start-mc-idp-ldap-update-desc

:mc:`mc idp ldap update` 命令用于修改 AD/LDAP 提供程序的现有配置集。

.. end-mc-idp-ldap-update-desc

.. tab-set::

   .. tab-item:: 示例

      以下示例修改 ``myminio`` 部署的两个 AD/LDAP 配置项。

      .. code-block:: shell
         :class: copyable

         mc idp ldap update                                \
                     myminio                               \
                     lookup_bind_dn=cn=admin,dc=min,dc=io  \
                     lookup_bind_password=somesecret

   .. tab-item:: 语法

      该命令使用以下语法：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] idp ldap update           \
                                   ALIAS            \
                                   [CFG_PARAM1]     \
                                   [CFG_PARAM2]...

      - 将 ``ALIAS`` 替换为要更新 AD/LDAP 集成配置的 MinIO 部署的 :ref:`alias <alias>`。
      - 将 ``[CFG_PARAM#]`` 替换为各个 :ref:`configuration setting <minio-ldap-config-settings>` 键值对，格式为 ``PARAMETER="value"``。

      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax

参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   要修改其 AD/LDAP 集成配置的 MinIO 部署的 :ref:`alias <alias>`。

   例如：

   .. code-block:: none

      mc idp ldap update myminio                               \
                         lookup_bind_dn=cn=admin,dc=min,dc=io  \

.. include:: /includes/common-minio-ad-ldap-params.rst
   :start-after: start-minio-ad-ldap-params
   :end-before: end-minio-ad-ldap-params


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
