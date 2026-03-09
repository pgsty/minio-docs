.. _minio-mc-idp-ldap-accesskey-disable:

=================================
``mc idp ldap accesskey disable``
=================================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2


.. mc:: mc idp ldap accesskey disable


描述
----

.. start-mc-idp-ldap-accesskey-disable-desc

:mc:`mc idp ldap accesskey disable` 会在 MinIO 部署上禁用指定的 :ref:`access key <minio-id-access-keys>`。

.. end-mc-idp-ldap-accesskey-disable-desc

.. tab-set::

   .. tab-item:: 示例

         以下示例在 ``minio`` 部署上禁用访问密钥 ``mykey``：

      .. code-block:: shell
         :class: copyable

         mc idp ldap accesskey disable minio mykey

   .. tab-item:: 语法

      该命令的语法如下：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] idp ldap accesskey disable  \
                                          ALIAS       \
                                          KEY


      - 将 ``ALIAS`` 替换为已配置 AD/LDAP 集成的 MinIO 部署的 :ref:`alias <alias>`。
      - 将 ``KEY`` 替换为要禁用的访问密钥。
        
      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   已配置 AD/LDAP 的 MinIO 部署的 :ref:`alias <alias>`。

   例如：

   .. code-block:: none

         mc idp ldap accesskey disable minio

.. mc-cmd:: KEY
   :required:

   要禁用的已配置访问密钥。

示例
~~~~

禁用 ``minio`` 部署中的访问密钥 ``mykey``。

.. code-block:: shell
   :class: copyable

   mc idp ldap accesskey disable minio/ mykey

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
