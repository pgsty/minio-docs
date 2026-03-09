.. _minio-mc-idp-ldap-accesskey-enable:

================================
``mc idp ldap accesskey enable``
================================

.. default-domain:: minio

.. contents:: 目录
   :local:
   :depth: 2


.. mc:: mc idp ldap accesskey enable


描述
----

.. start-mc-idp-ldap-accesskey-enable-desc

:mc:`mc idp ldap accesskey enable` 在本地服务器上启用指定的 :ref:`access key <minio-id-access-keys>`。

.. end-mc-idp-ldap-accesskey-enable-desc

.. tab-set::

   .. tab-item:: 示例

         以下示例启用 ``minio`` 部署中的 access key ``mykey``：

      .. code-block:: shell
         :class: copyable

         mc idp ldap accesskey enable minio/ mykey

   .. tab-item:: 语法

      该命令具有以下语法：

      .. code-block:: shell
         :class: copyable

         mc [GLOBALFLAGS] idp ldap accesskey enable  \
                                          ALIAS      \
                                          KEY


      - 将 ``ALIAS`` 替换为配置了 AD/LDAP 集成的 MinIO 部署的 :ref:`alias <alias>`。
      - 将 ``KEY`` 替换为要启用的 access key。
        
      .. include:: /includes/common-minio-mc.rst
         :start-after: start-minio-syntax
         :end-before: end-minio-syntax


参数
~~~~

.. mc-cmd:: ALIAS
   :required:

   配置了 AD/LDAP 的 MinIO 部署的 :ref:`alias <alias>`。

   例如：

   .. code-block:: none

         mc idp ldap accesskey enable minio mykey

.. mc-cmd:: KEY
   :required:

   要启用的已配置 access key。

示例
~~~~

启用 ``minio`` 部署中的 access key ``mykey``。

.. code-block:: shell
   :class: copyable

   mc idp ldap accesskey enable minio/ mykey

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
